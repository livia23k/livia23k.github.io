---
title: Security | Security Architecture
categories: [computer science, security]
tags: [tech, computer security, 23fall, 18-730, review]
math: True
img_path: /assets/img/post/2023-11-19-cryptoarch/
---

## 1. Architecture for Software System

### 1.1 Benefits

- simplifies Threat and Attack analysis 
- allows High Assurance
    > E.g., Formal Verification of Security Properties 
- limits effects of successful Adversary Attacks 
- facilitates Trusted Recovery from failures

### 1.2 Security Policies

#### 1.2.1 Security Boundary (Trusted Computing Bases, TCB)

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


#### 1.2.2 Access Control Policy

##### Utility

1. Avoid circumvention of discretionary control over object/app service sharing
> strategy: using Access Control List (ACL) based Discretionary Access Control (DAC)

2. Avoid circumvention of Non-discretionary control over objects/services
> strategy: access authorization not own/control by user, but by organization/enterprice policies

##### Property

* Access Control is “separable” from Kernel/TCB Penetration Resistance.
> change one of each does no need to change the other.

##### DAC

[saving space]

##### MAC

[saving space]


#### 1.2.3 Audit (Control Policy)

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


#### 1.2.4 (User) Authentication

##### Utility

Avoid User Impersonation
> key primitive requited by all other security mechanisms

##### Property

* Authentication is “separable” from Kernel/TCB Penetration Resistance.
> change one of each does no need to change the other.

#### 1.2.5 Relationships

![](depend.png){: w="600"}

### 1.3 Native Operating System

#### 1.3.1 Structure Overview

![](native.png){: w="600"}

#### 1.3.2 (Native) Security Kernel

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


#### 1.3.3 (Native) Microkernels

##### Structure

DBMS(ref. mon.), E-mail System(ref. mon.)

---------------------------------(Trusted OS Servers, Legacy OSs)------------------------- Traditional TCB ⬇️

Network System(ref. mon.), File System(ref. mon.)

VM Segments(Access Policy Server), I/O System(Access Policy Server)

login

-----------------------------------------(Resource Manager)-------------------------------  Security Kernel ⬇️

Address Spaces, Treads, Inter-Process Communication (IPC)

Untyped Objects, static kernel memory


### 1.4 Virtualization

#### 1.4.1 Structure Overview

![](virtual.png){: w="600px"}

#### 1.4.2 Benefit

Has very small attack boundaries.

#### 1.4.3 History & Now

![](virtualcmp.png){: w="800px"}


## 2. Architecture for Application Isolation

### 2.1 Separation Kernel

#### 2.1.1 Fined Structure

![](virtual2.png){: w="600px"}

#### 2.1.2 Hardware Abstraction

**Illustration**

![](sepkill.png){: w="200px"}

**(Sepeation Kernel) Partitions**

1. Data isolation
> Data inaccessible from outside partitions

2. Information-Flow isolation
> Information flows between partitions are only via controlled channels
    - periods processing
    > By strictly defining the executing time of each partition, it could prevent data leak to other partitions.

3. Fault isolation
> Effects of faults in a partition does not spread to other partitions.

- Pros & Cons

    - minimizes size/form-factor, weight, power 
    - minimizes exposed communication-network fabric
    - maximizes partition switching speed

**Controlled Channels**

Compositions:

Exist between two partitions

- Port
> a restricted Read-only extension to the state of the Destination partition;

- Source
> pointing to the communication source, is able to read & write port;

- Destination
> arrow identifying the partition whose state is extended
    
Properties:

- Unidirectional
> no (limited) information flow from Destination to Source

- Confidential
> only destination could read port

- Authentic
> only source could write port

#### 2.1.3 Other Separation Abstraction

- Physical
> implemented with physically separate resources and command lines

![](phypart.png){: w="200px"}

- Cryptographic
> Encryption, authentication and authenticated encryption of stored data and communication messages

- Static Analysis
> Program analysis to guarantee information flow from one program to another;
> 
> requires closure of all programs in a system

#### 2.1.4 Partitioning Communication System

![](partsys.png){: w="450px"}

**Policy**

Pump:
> A pump is a trusted program in a separate partition that moves data from a low security level to a high security level without returning acknowledgements except in a controlled way; e.g., very slowly, in batches.

Trusted Guard:
> A guard is a trusted program in a separate partition that moves data from a high security level to a law security level by removing (i.e., scrubbing) all sensitive information or by creating a cover story (i.e., modifying data with non-sensitive information).

Crypto Separation:
> Classifying sensitive plaintext (red) and ciphertext (black). Ensure no red data transferred to black (untrusted) partition.
> 
> Adding header and encrypt the plaintext (red) to a ciphertext (black). Then could transfer it to black partition.

#### 2.1.5 Application

Applicable:

1. Minimal hypervisor
> but not for Virtual Machine Monitors

2. Multi-level Security Application
> communication between high and low level application

3. Red-Black Crypto Controller
> The red-black separation is intended to transform and move red (classified) data to black (unclassified) data by trusted message encryption and trusted header-information control. That is no information from the red partition can move to the black partition.

    need four partitions
    > red, header, encryption, black

    need four channels for communication 
    > red-> header, red -> encryption, header -> black and encryption -> black
    
    ![](rbcc.png){: w="400px"}

Not applicable:

1. Commodity workstations
> complex, out-of-date assurance

### 2.2 Security Kernel

#### 2.2.1 Properties

1. Isolated
> have only several entry points where kernel could transfer control only to the caller
> 
> kernel entry/call parameters must be checked when passed by value
>
> timing consistency of access checks (Time-of-check-to-time-of-use atomicity)

2. Complete reference mediation
> all references to an object by a user process/program must be checked for the permission allowed by the access-control policy component of the kernel
>
> applied by any policy

3. Small enough to be verifiable
> does not contain large systems like file system and dir. system

### 2.3 Separation Kernels vs. Security Kernels vs. VM Monitors

![](sepkseck.png){: w="400px"}

![](sepkvmm.png){: w="400px"}


## 3. Security Primitives

### 3.1 Root of Trust Management

#### 3.1.1 Concepts

**Root of Trust**

A source that can always be trusted within a cryptographic system.

**Secure Boot**

$$Sign_{K_{priv}}(sw_1, sw_2, ... , s)$$

Boot using only trusted software needed for booting (not checking any other firmwares …), check all signatures of these software, if all valid, then firmware gives control to os.

Ensure the system boot with all trusted software.

> reference: https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-secure-boot

**Trusted Boot**

When the system first booted, log the trusted software info on a "hash table", later verify the integrity of all components of the boot sequence depending on the "hash table".

Ensure the trusted softwares are not modified.

Possible for boot-then-verify.

**Sealed Storage (AE with AD)**

> AE means Authenticated Encryption
> 
> AD means Associated Data
>
> My understanding is: the storage is sealed, not modifiable. Every time we use the trusted boot to verify the data, the result is dependable because of the sealed storage.

**Remote Attestation**

> Verify the integrity and authenticity of a software system remotely.
> 
> Because the random nonce is used, it can ensure that the attestation is fresh and specific to the current session.

#### 3.1.2 SRTM vsv DRTM

##### SRTM Shortcomings

- Coarse-grained, measures entire system 

    – requires hundreds of integrity measurements just to boot

    – every host is different 
    > firmware versions, drivers, patches, apps, spyware, …

    – what does a PCR mean in this context? 
    > PCR - Platform Configuration Register
    >
    > used in Trusted Platform Module (TPM), which is the "hash table" mentioned before (at 3.1.1 Trusted Boot)
    >
    > act as another wrapper of hash of origin TPM items, then store it back to TPM

    – TCB includes entire system! 

- Integrity measurements are done at load-time not at run-time 

    – Time-of-check-time-of-use (TOCTOU) problem 

    – Cannot detect any dynamic attacks! 

    – No guarantee of execution

##### SRTM Example

![](srtmeg.png){: w="600px"}

> Explaination of each abbrevation:
> 
> BIOS: Basic Input/Output System, the program a personal computer's microprocessor uses to get the computer system started after you turn it on.
> 
> ROT: Root of Trust, a set of functions in the trusted computing module that is always trusted by the computer's operating system (OS).
>
> CRTM: Core Root of Trust for Measurement, the first piece of code to execute when a computer starts, and it measures the BIOS.
>
> POST: Power-On Self-Test, a process performed by firmware or software routines immediately after a computer or other digital electronic device is powered on.
>
> TPM: Trusted Platform Module, a specialized chip on an endpoint device that stores RSA encryption keys specific to the host system for hardware authentication.
> 
> PCR: Platform Configuration Register, used in TPM for storing the measurements (hashes) that allow a system to check if a system has been tampered with.
> 
> GRUB Stage1 (MBR): Grand Unified Bootloader, a package that allows a user to select from multiple operating systems every time the computer starts up. Stage1 or MBR refers to the Master Boot Record, the first stage of the GRUB bootloader.
> 
> GRUB Stage1.5: An intermediate stage in some GRUB installations that bridges the gap between the MBR and the main GRUB bootloader.
>
> GRUB Stage2: The main stage of the GRUB bootloader, where the menu and the larger part of the bootloader's functionality reside.
>
> Operating System: The software that, after being initially loaded into the computer by a boot program, manages all the other programs in a computer.
> 
> /usr/sbin/httpd: The location of the HTTP daemon in Unix-based systems, which is the program that acts as the web server.
> 
> /bin/ls: A common directory path in Unix-based systems that contains the 'ls' command, which lists directory contents.
> 
> Linux Kernel: The core of the Linux operating system, which manages the machine's hardware and provides services to the user-space applications.


##### Dynamic Root of Trust Management

- Security properties similar to reboot 
    
    – without a reboot! 
    
    – late launch of a measured block of code 
    
    – removes many components from TCB 
    > BIOS, boot loader, DMA-enabled devices, …
    >
    > long-running OS and Apps, if done right 
    
- Integrity of loaded code (only) can be attested 

- Untrusted legacy OS can coexist with trusted software 

- Allows introduction of new, higher-assurance software without breaking existing systems

##### DRTM Example

- SKINIT instruction in AMD CPU 
    – Late launches a Secure Loader Block (SLB) 

- SKINIT executes atomically 

    – Sets CPU state similar to INIT (soft reset) 
    
    – Disables interrupts 
    
    – Enables DMA protection for entire 64 KB SLB 
    
    – Record the hash of SLB in TPM 
    
    – Begins executing SLB 
    
- Remote Attestation: Verifier receives attestation after SKINIT 

    – Knows SKINIT was used (hash chain 00… vs ff…) 
    
    – Knows software TCB includes only the SLB 
    
    – Knows exactly what SLB was executed

> SKINIT is used to launch Secure Loader Block, which is used to initiate a secure and trusted environment for critical software to run, like the environment for DRTM.
> 
> It executes atomically, ensuring the integrity of the security process it initiated.
