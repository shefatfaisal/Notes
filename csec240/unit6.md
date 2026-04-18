
to create a process we need to use fork() system call

when parent call fork() system call a child process is created 

for parent process fork() returns child pid

but for children process fork() returns 0 to uniquely identify that this is child

if fork() returns -1 then fork failed to create new child process




**Notes from chatGPT**


## Fork summary

- `fork()` is a **system call** that creates a **new process** (child) from the current process (parent).
    
    Week5_LinuxProgramming
    
- After `fork()`, **both processes continue from the next line** in the same program.
    

## Return value (stored in `pid_t pid`)

- In the **child**: `pid == 0`
    
- In the **parent**: `pid == (child’s PID)` (a positive number)
    
- On **failure**: `pid == -1`
    

## What is `pid_t`?

- A type used to store PIDs (usually an integer type), defined by the OS.
    

## Memory behavior

- Child starts as a near-clone of parent’s **virtual memory**.
    
- Parent and child often show the **same virtual addresses** (like `&x`).
    
- Linux uses **copy-on-write**:
    
    - Initially they may share the same physical pages
        
    - When one process writes, the kernel copies that page, so changes don’t affect the other
        

## Virtual vs physical memory

- **Virtual address**: what the process sees (its own private address space)
    
- **Physical address**: real RAM location
    
- Same virtual address in two processes does **not** guarantee same physical RAM (except temporarily right after fork due to COW).
    

## Fork in loops

- If every process keeps forking, process count grows fast.
    
- Rough rule: after `n` forks in a straight line → about **2^n** total processes.



**Virtual vs physical**
## The simplest picture

### Physical memory (RAM)

- The real hardware memory sticks.
    
- Think: a big set of numbered “slots” the machine actually has.
    

### Virtual memory

- Each process gets its own “pretend map” of memory addresses.
    
- Your C program uses these addresses (like `0x7ffe...`) but they are **not raw RAM locations**.
    
- The OS + CPU translate virtual → physical.
    

## The translator: page tables

When your program accesses address `0x7ffecff6aa10`:

1. CPU looks up the process’s **page table**
    
2. Finds which **physical RAM page** that virtual page maps to
    
3. Accesses that physical RAM
    

So the same virtual address:

- in process A can map to physical page 100
    
- in process B can map to physical page 900
    

That’s why processes are isolated.

## Why we even use virtual memory

- **Isolation**: one process can’t (normally) read/write another’s memory
    
- **Convenience**: each process can “think” it has a large continuous memory space
    
- **Sharing**: OS _can_ map the same physical page into multiple processes when useful (shared libraries, copy-on-write after fork)
    
- **Swap**: if RAM is tight, some virtual pages can live on disk temporarily
    

## Connect it to your fork experiment

You saw both parent and child print the same `&x`.  
That means:

- same **virtual** address in both  
    But the parent stayed at `x=10` while child changed to 20:
    
- after the write, they must have ended up using **different physical pages** for that memory (copy-on-write).
    

---

One quick check (one question):  
If I open **two different programs** (say Firefox and Terminal), can both have a variable at virtual address `0x7ffecff6aa10` at the same time? (yes/no)

yes

Yes ✅ and that’s the “wow” moment.

Even if both programs use the _same-looking_ virtual address, they’re in different processes, so their page tables can map that address to different physical RAM.

Mini mental model you can keep:

- **Virtual address** = apartment number (can repeat in different buildings)
    
- **Physical address** = actual GPS location of the building





so kernel keeps the child process record untill parent call wait

Yes — that’s exactly it ✅

When the child exits, the kernel keeps a small record (PID + exit status + a bit of accounting info) **until the parent calls `wait()` / `waitpid()`**. While it’s waiting to be reaped, the child shows as a **zombie (Z)**.

Two quick notes so your mental model is accurate:

- It’s **not** keeping the child’s whole memory/code anymore — just the minimal “exit record.”
    
- If the parent **never** waits, zombies can accumulate (bad). If the parent dies, `init/systemd` adopts the child and eventually reaps it.


if parent disappear before calling wait() then init/systemd pid 1 adopted the orphaned child then pid1 will reap (destroy call wait() for it) so the zombie children get cleaned up

- Parent dies → child becomes **orphan**
    
- Orphan gets adopted by **PID 1**
    
- PID 1 reaps it → zombie disappears



**execl, execp, execv**
- `execl("/bin/ls", "ls", "-l", NULL);`
    
- `execlp("ls", "ls", "-l", NULL);` ✅ searches PATH
    
- `execv("/bin/ls", argv);`
    
- `execvp("ls", argv);` ✅ searches PATH


### Do the 4 functions do the same work?

They all do the same _core_ thing: **replace the current process with a new program** (exec).  
They differ only in **how you pass arguments** (`l` vs `v`) and whether they **search PATH** (`p` vs no `p`).

### Shell behavior (your understanding is on point)

When you type a command in a shell like:

`ls -l`

The shell generally does:

1. **Parent (the shell process)** calls `fork()` → creates a **child**
    
2. **Child** calls an `exec*()` function → becomes the command (`ls`)
    
3. **Parent** usually calls `wait()` → waits until the command finishes
    
4. Then shell prints the next prompt
    

So:

- The **shell stays the parent** (it does not turn into `ls`)
    
- The **child turns into `ls`** via `exec`
    

Most shells use something like `execve()` under the hood (or wrappers like `execvp`
