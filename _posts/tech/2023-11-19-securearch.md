---
title: Security | Security Architecture
categories: [computer science, security]
tags: [tech, computer security, 23fall, 18-730, review]
math: True
img_path: /assets/img/post/2023-11-19-cryptoarch/
---

## Architecture for Software System

### Benefits

- simplifies Threat and Attack analysis 
- allows High Assurance
    > E.g., Formal Verification of Security Properties 
- limits effects of successful Adversary Attacks 
- facilitates Trusted Recovery from failures

### Security Policies

#### Security Boundary (Trusted Computing Bases, TCB)

##### Strategies

Set security boundary (TCB) to OS kernel.

Security checks needed for penetration resistance:

1. Isolation Checks

    - entry and return point control
        > ensuring it is not modified by user process

    - parameter checking on every kernel call
        - for values, length not exceeding
        - for reference, caller has permission to access address in kernel

2. Complete Reference-Mediation Checks

    - Mandatory reference mediation
        > references to any project needed to gain permission of Complete Reference-Mediation Checks

3. Timing Consistency of Access Checks (Time-of-Check-To-Time-of-Use atomicity, TOCTTOU)

    - atomicity (when checking access permission and executing that operation, the kernel/TCB can not be interrupted)

4. Reference Mediation for Kernel/TCB internal variables and functions

    - for Integrity and Recovery in a Secure State
        - variable not alterable by untrusted user process; alternation must not overflow or cause duplicate entries
        - resources limits should be enforced by Kernel/TCB
        - TOCTTOU and sequencing of internal non-atomic functions
    - for Mandatory Secrecy Policie
        - internal kernel/TCB variables may not be visible to an untrusted process

5. Kernel/TCB Isolation from Untrusted User/Application behavior

    - dependencies often cause to Denial of Service (DoS);
        > E.g. or the unprivileged user could invoke the “panic/reboot” function, causing the other priviledged users requests be denied consistently.

6. Avoid Cyclic Dependencies


##### Attack

<EP, DI, CH> = <entry point, data item, channel>

1. Buffer Overflow Attack

    > Return-Oriented Programming Attack, by passing an over-length string variable, change the return address. Then the adversary could access the chosen program in kernel address. (Same as 18-613 Attack Lab)

2. Failure verifying parameter address on kernel call

    > ustat(dev, buf), invoke "copyseg" to overwrite separate address space

3. ...


#### Access Control Policy

##### Utility

1. Avoid circumvention of discretionary control over object/app service sharing
> strategy: using Access Control List (ACL) based Discretionary Access Control (DAC)

2. Avoid circumvention of Non-discretionary control over objects/services
> strategy: access authorization not own/control by user, but by organization/enterprice policies

##### Property

* Access Control is “separable” from Kernel/TCB Penetration Resistance.
> change one of each does no need to change the other.


#### Audit (Control Policy)

##### Utility

1. Avoid circumvention of Policy by Authorized/Trusted Users
    - accountability primitive: non-repudiation; fair and complete;
    - deterrent against untrustworthy behavior by trusted users/admin;
    - no intrusion/penetration detection role;

2. Circumvention of Authentication Policy

##### Auditable Events

1. Determined by (Privileged) ACP; i.e., security-state transitions
> E.g., DAC, MLS, MILS, RBAC, SoD; Chinese Wall

2. Determined by Authentication policy
> E.g. system entry and exit events

##### Property

* Audit is “separable” from Kernel/TCB Penetration Resistance.
> change one of each does no need to change the other.


#### (User) Authentication

##### Utility

Avoid User Impersonation
> key primitive requited by all other security mechanisms

##### Property

* Authentication is “separable” from Kernel/TCB Penetration Resistance.
> change one of each does no need to change the other.

#### Relationships

![](depend.png){: w="600"}

### Native Operating System

#### Structure Overview

![](native.png){: w="600"}

#### (Native) Security Kernel

##### Property

1. Isolated
> cannot be interfered with or changed

2. Complete reference mediation
> non-circumventable, non-bypassable, always invoked

3. Small and simple enough to be verifiable

##### Structure

DBMS(ref. mon.), E-mail System(ref. mon.)

-------------------------(Reference Monitor + Services)------------------------------------ Traditional TCB ⬇️ 

Network System(ref. mon.), File System (ref. mon.)

Dir. System

login, audit, SSA

-----------------------(Reference Monitor + Access Policy)--------------------------------  Security Kernel ⬇️ 

VM Segments, Processes & Inter-Process Communication (IPC), Basic I/O


#### (Native) Microkernels

##### Structure

DBMS(ref. mon.), E-mail System(ref. mon.)

---------------------------------(Trusted OS Servers, Legacy OSs)------------------------- Traditional TCB ⬇️

Network System(ref. mon.), File System(ref. mon.)

VM Segments(Access Policy Server), I/O System(Access Policy Server)

login

-----------------------------------------(Resource Manager)-------------------------------  Security Kernel ⬇️

Address Spaces, Treads, Inter-Process Communication (IPC)

Untyped Objects, static kernel memory


### Virtualization

#### Structure Overview

![](virtual.png){: w="600px"}

#### Benefit

Has very small attack boundaries.

#### History & Now

![](virtualcmp.png){: w="800px"}


## Architecture for Application Isolation

[saving space]
