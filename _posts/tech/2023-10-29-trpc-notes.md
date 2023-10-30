---
title: Web Dev - tRPC Notes
categories: [computer science, web development]
tags: [tech, web dev, backend, learning note]
---

## 1. tRPC

### Introduction

> tRPC stands for Typescript Remote Procedure Call. Instead of producing an API definition for your back end with something like OpenAPI or GraphQL, tRPC directly infers and applies your TypeScript endpoints. It also applies the client-side parameters to the server.
>
> [Intro to tRPC: Integrated, full-stack TypeScript](https://www.infoworld.com/article/3690275/intro-to-trpc-integrated-full-stack-typescript.html#:~:text=tRPC%20stands%20for%20Typescript%20Remote,side%20parameters%20to%20the%20server.)

### Generic Endpoint

tRPC will take over all the requests here

```ts
// @/pages/api/trpc/[trpc]/route.ts

import { fetchRequestHandler } from "@trpc/server/adapters/fetch"
import { type NextRequest } from "next/server"

import { appRouter } from "@/server/api/root"
import { createTRPCContext } from "@/server/api/trpc"

const handler = (req: NextRequest) =>
	fetchRequestHandler({
		endpoint: "/api/trpc",
		req,
		router: appRouter,
		createContext: () => createTRPCContext({ req }),
		onError:
		process.env.NODE_ENV === "development"
			? ({ path, error }) => {
				console.error(
					`❌ tRPC failed on ${path ?? "<no-path>"}: ${error.message}`
				)
			}
			: undefined,
	})

export { handler as GET, handler as POST }
```


### tRPC content

the true handler

**createInnerTRPCContext**
 - the auth context (user info)

What?
	the context includes user's session and database access

Why?
	by including the session in the context, you can easily access user-related information in your tRPC procedures, which can be crucial for determining permissions and roles.

**createTRPCContext**
- real context in tRPC and API call exchanges

How?
	1. take a req object (type: NextRequest), which means the incoming HTTP request and contains various properties like headers and body, as argument
	2. call createInnerTRPCContext to generate the inner context, setting up shared resources
	3. return inner context, which could be globally accessible by all tRPC API, (then using the router (introduced later) to determine the related API, then handle it and return the result)

**t**
- setting up tRPC environment
- tRPC APIs are initialized here
- how to handle error and data transformation is also here

then we could use this environment to create routers and procedures

**enforceUserIsAuthed**
- the middleware that enforces that a user must be logged in to access certain procedures
- it is used by **protectedProcedure**


code sample from [t3dotgg/chirp](https://github.com/t3dotgg/chirp)

```ts
// @/server/api/trpc.ts

/**
 * YOU PROBABLY DON'T NEED TO EDIT THIS FILE, UNLESS:
 * 1. You want to modify request context (see Part 1).
 * 2. You want to create a new middleware or type of procedure (see Part 3).
 *
 * TL;DR - This is where all the tRPC server stuff is created and plugged in. The pieces you will need to use are documented accordingly near the end.
 */

import { initTRPC, TRPCError } from "@trpc/server"
import { type NextRequest } from "next/server"
import superjson from "superjson"
import { ZodError } from "zod"
import { getServerAuthSession } from "@/server/auth"
import { db } from "@/server/db"

/**
 * 1. CONTEXT
 *
 * This section defines the "contexts" that are available in the backend API.
 *
 * These allow you to access things when processing a request, like the database, the session, etc.
 */

interface CreateContextOptions {
  headers: Headers
}

/**
 * This helper generates the "internals" for a tRPC context. If you need to use it, you can export it from here.
 *
 * Examples of things you may need it for:
 * - testing, so we don't have to mock Next.js' req/res
 * - tRPC's `createSSGHelpers`, where we don't have req/res
 *
 * @see https://create.t3.gg/en/usage/trpc#-serverapitrpcts
 */
export const createInnerTRPCContext = async (opts: CreateContextOptions) => {
  const session = await getServerAuthSession()

  return {
    session,
    headers: opts.headers,
    db,
  }
}

/**
 * This is the actual context you will use in your router. It will be used to process every request that goes through your tRPC endpoint.
 *
 * @see https://trpc.io/docs/context
 */
export const createTRPCContext = async (opts: { req: NextRequest }) => {
  // Fetch stuff that depends on the request

  const innerContext = await createInnerTRPCContext({
    headers: opts.req.headers,
  })
  return innerContext
}

/**
 * 2. INITIALIZATION
 *
 * This is where the tRPC API is initialized, connecting the context and transformer. We also parse
 * ZodErrors so that you get typesafety on the frontend if your procedure fails due to validation
 * errors on the backend.
 */

const t = initTRPC.context<typeof createTRPCContext>().create({
  transformer: superjson,
  errorFormatter({ shape, error }) {
    return {
      ...shape,
      data: {
        ...shape.data,
        zodError:
          error.cause instanceof ZodError ? error.cause.flatten() : null,
      },
    }
  },
})

/**
 * 3. ROUTER & PROCEDURE (THE IMPORTANT BIT)
 *
 * These are the pieces you use to build your tRPC API. You should import these a lot in the
 * "/src/server/api/routers" directory.
 */

/**
 * This is how you create new routers and sub-routers in your tRPC API.
 *
 * @see https://trpc.io/docs/router
 */
export const createTRPCRouter = t.router

/**
 * Public (unauthenticated) procedure
 *
 * This is the base piece you use to build new queries and mutations on your tRPC API. It does not
 * guarantee that a user querying is authorized, but you can still access user session data if they
 * are logged in.
 */
export const publicProcedure = t.procedure

/** Reusable middleware that enforces users are logged in before running the procedure. */
const enforceUserIsAuthed = t.middleware(({ ctx, next }) => {
  if (!ctx.session || !ctx.session.user) {
    throw new TRPCError({ code: "UNAUTHORIZED" })
  }
  return next({
    ctx: {
      // infers the `session` as non-nullable
      session: { ...ctx.session, user: ctx.session.user },
    },
  })
})

/**
 * Protected (authenticated) procedure
 *
 * If you want a query or mutation to ONLY be accessible to logged in users, use this. It verifies
 * the session is valid and guarantees `ctx.session.user` is not null.
 *
 * @see https://trpc.io/docs/procedures
 */
export const protectedProcedure = t.procedure.use(enforceUserIsAuthed)
```


### App Router

overall config of routers

```ts
// @/server/api/root.ts

import { createTRPCRouter } from "@/server/api/trpc"
import { trainingRouter } from "./routers/training"

/**
* This is the primary router for your server. All routers added in /api/routers should be manually added here.
*/

export const appRouter = createTRPCRouter({
	training: trainingRouter,
})

// export type definition of API
export type AppRouter = typeof appRouter
```

single router, where to define the hooks we need

```ts
// @/server/api/routers/xxx.js

import { z } from "zod"
import { createTRPCRouter, protectedProcedure } from "@/server/api/trpc"

export const xxxRouter = createTRPCRouter({
	// Procedure Types: https://trpc.io/docs/server/procedures
	create: protectedProcedure
	// Authenticate input using zod
	.input (
		// Zod Doc: https://zod.dev/?id=basic-usage
		z.object(
			{
				column1: z.string(),
				column2: z.any(),
				column3: z.string().optional(),
				...
			}
		)
	)
	// Hooks
	// TRPC Mutation Doc: https://trpc.io/docs/client/react/useMutation
	// React Mutation Doc: https://tanstack.com/query/v4/docs/react/guides/mutations
	.mutation(async ({ ctx, input }) => {
		const { column1, column2, column3, ... } = input

		const xxx = await ctx.db.tableXXX.create({
			data : {
				column1: ,
				column2: ,
				column3: ,
				// Examples:
				// userId: ctx.session!.user.id,
				// createdAt: new Date(),
			}
		})
	})

})
```


## 3. React query on tRPC

```ts
// @/pages/api/xxx.tsx

"use client"
import { api } from "@/trpc/react"
import { Button } from "@chakra-ui/react"

export default function TestingPage() {
  const createXXX = api.training.create.useMutation()

  return (
    <Button
      onClick={() =>
        createTrainingSession.mutate({
          column1: "test",
          column2: "test",
          column3: "test",
        })
      }
    >
      Create Training Session
    </Button>
  )
}

```

## 4. Other related config

### 4.1 Auth

a sample (from [danba340/full-stack-t3-tutorial]([]https://github.com/danba340/full-stack-t3-tutorial))

```ts
import type { GetServerSidePropsContext } from "next";
import {
  getServerSession,
  type NextAuthOptions,
  type DefaultSession,
} from "next-auth";
import EmailProvider from "next-auth/providers/email";
import { PrismaAdapter } from "@next-auth/prisma-adapter";
import { prisma } from "./db";

/**
 * Module augmentation for `next-auth` types
 * Allows us to add custom properties to the `session` object
 * and keep type safety
 * @see https://next-auth.js.org/getting-started/typescript#module-augmentation
 **/
declare module "next-auth" {
  interface Session extends DefaultSession {
    user: {
      id: string;
      // ...other properties
      // role: UserRole;
    } & DefaultSession["user"];
  }

  // interface User {
  //   // ...other properties
  //   // role: UserRole;
  // }
}

/**
 * Options for NextAuth.js used to configure
 * adapters, providers, callbacks, etc.
 * @see https://next-auth.js.org/configuration/options
 **/
export const authOptions: NextAuthOptions = {
  callbacks: {
    session({ session, user }) {
      if (session.user) {
        session.user.id = user.id;
        // session.user.role = user.role; <-- put other properties on the session here
      }
      return session;
    },
  },
  adapter: PrismaAdapter(prisma),
  providers: [
    EmailProvider({
      server: {
        host: process.env.EMAIL_SERVER || "http://localhost:3000",
        port: 587,
        auth: {
          user: "apikey",
          pass: process.env.EMAIL_API_KEY,
        },
      },
      from: process.env.EMAIL_FROM || "test@localhost.com",

      ...(process.env.NODE_ENV !== "production"
        ? {
            sendVerificationRequest({ url }) {
              console.log("LOGIN LINK", url);
            },
          }
        : {}),
    }),
    /**
     * ...add more providers here
     *
     * Most other providers require a bit more work than the Discord provider.
     * For example, the GitHub provider requires you to add the
     * `refresh_token_expires_in` field to the Account model. Refer to the
     * NextAuth.js docs for the provider you want to use. Example:
     * @see https://next-auth.js.org/providers/github
     **/
  ],
};

/**
 * Wrapper for getServerSession so that you don't need
 * to import the authOptions in every file.
 * @see https://next-auth.js.org/configuration/nextjs
 **/
export const getServerAuthSession = (ctx: {
  req: GetServerSidePropsContext["req"];
  res: GetServerSidePropsContext["res"];
}) => {
  return getServerSession(ctx.req, ctx.res, authOptions);
};
```

### 4.2 Prisma

#### Client

prisma client

```ts
// @/server/db.ts

import { PrismaClient } from "@prisma/client"

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined
}

export const db =
  globalForPrisma.prisma ??
  new PrismaClient({
    log:
      process.env.NODE_ENV === "development"
        ? ["query", "error", "warn"]
        : ["error"],
  })

if (process.env.NODE_ENV !== "production") globalForPrisma.prisma = db
```

#### Schema

```ts
// This is your Prisma schema file,
// learn more about it in the docs: https://pris.ly/d/prisma-schema

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mongodb" // change it to other db if needed
  url      = env("DATABASE_URL")
}

// Model Examples
model User {
  id        Int      @id @default(autoincrement()) @map("_id") @db.ObjectId
  createdAt DateTime @default(now())
  email     String   @unique
  name      String?
  role      Role     @default(USER)
  posts     Post[]
}

model Post {
  id        Int      @id @default(autoincrement()) @map("_id") @db.ObjectId
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  published Boolean  @default(false)
  title     String   @db.VarChar(255)
  author    User?    @relation(fields: [authorId], references: [id])
  authorId  Int?     @db.ObjectId
}

enum Role {
  USER
  ADMIN
}
```



## References

0. [Intro to tRPC: Integrated, full-stack TypeScript](https://www.infoworld.com/article/3690275/intro-to-trpc-integrated-full-stack-typescript.html#:~:text=tRPC%20stands%20for%20Typescript%20Remote,side%20parameters%20to%20the%20server.)
1. [T3 Stack Tutorial - FROM 0 TO PROD FOR $0 (Next.js, tRPC, TypeScript, Tailwind, Prisma & More)](https://www.youtube.com/watch?v=YkOSUVzOAA4&t=7668s)
2. [t3dotgg/chirp](https://github.com/t3dotgg/chirp)
3. [danba340/full-stack-t3-tutorial](https://github.com/danba340/full-stack-t3-tutorial)
4. [Zod Doc](https://zod.dev/)
5. [tRPC Doc](https://trpc.io/docs)