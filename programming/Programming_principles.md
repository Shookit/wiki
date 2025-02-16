Programming principles
======================


Design recommendations
----------------------

### Linting

Use a [lint](programming/Linting.md) program to assist with code best practices.
Make sure this is run as a pre-commit hook in git, and in e.g., Jenkins, not manually.


### Naming

Naming is hard. Remember, the entire description of the function/variable doesn't have to be in the name - you can use parameter names, types, and return types to add to the description of a function in static languages. Don't repeat the type in the name.


### Function design

When determining the arguments and return for a function, remember, the arguments must make obvious, logical sense with what the function is actually doing. You must be able to get some guess as to what the function does just by reading the arguments. Also, keep functions as pure as possible: push network/database/disk/logging (side-effects in general) IO to outside the function as arguments whenever possible.


### Exceptions

- Checked vs unchecked (primarily a Java construct)
- "Throw early, catch late"


### DAO

Having a central location for database accesses (DAO) is really nice for refactorability and helping testability

### Database sessions

Everything inside a session (including queries and adds) are thread-local until you run a flush. When you flush, then it announces your transaction to the database, and it gets held until commit or roll back.


### Lambda functions

- Lambda comes from the Lambda Calculus and refers to anonymous functions in programming.
- Can be useful, but arent' reusable; often preferable to create a named function


### Relational database design

- Any time you have a many-to-one or many-to-many relationship, it should almost always be represented as two separate tables w/ a foreign key
- Otherwise, you run the risk of data duplication, data inconsistency, and other problems caused by denormalized databases

### State machines

If making something which behaves like a state machine, design a damn state machine!

### Adapters

Always keep in mind: "what if I wanted to replace this API/library/module with another one?
Avoid calling these directly, and instead use an adapter; thus, you just have to reimplement the adapter, instead of redoing your entire codebase.

Any time you check or modify anything (a boolean flag, a class property, a constant) more than once, it should be in a function/method, or you will have make changes all over the place (e.g., isDeleted, isActive) when things change


### Constants

Generally break out constant string/number usages to constant variables


### Microservices

If you can't shut down one microservice without impacting another, you likely didn't architect them properly.

Don't use a ridiculous master database; each service should get its own


Database vs filesystem
----------------------

Databases are amazing for running queries that return tiny portions of the full database.
To elaborate, if your query returns 50% of the contents of a large table, it's going to be excruciatingly slow (even if indexed).
However, if your query is only 0.1% of the content of the table, it's going to be relatively super fast.
Situations where you are scanning a significant part of a large table should either be 1) significantly refactored or 2) use the filesystem instead.

File systems are great for scanning a ton of data sequentially, but are bad at jumping to just a few locations in the file to pluck little bits of random data out.
Object stores like s3 or ceph can be good ways of availing this data to be scanned in a filesystem-like manner.


Simple terms and definitions
----------------------------

### Business software

- IaaS = just machines
- PaaS = you provide the application, they provide DB and libs (AWS)
- SaaS = software as a service
- BaaS = backend as a service, connects mobile apps to cloud


### Frame and stack pointers

- The stack pointer points to the junction between the extent of the stack and the start of the free space.
- The frame pointer gives a constant offset reference for the duration of each subroutine. It is placed in a manner where it separates parameters/arguments from the stack.


### TCP latency

TCP uses 'Nagles Algorithm', which stalls a not-completely-full-TCP-packet-send by up to ~200ms, which allows the packet to eventually get filled up. TCP has big packet overhead. There is a TCP flag which can disable this for latency-critical applications (e.g., webserver/browser or terminals), but is on by default. Consider using UDP instead.


### Serialization

- Method of turning a data structure into a string of data/bytes, which can be transmitted over a socket, file, or stdin/out.
- Deserialization is when you turn that string of bytes back into the original data structure.


### Security

- Authentication = "authentic" = are who you say you are = passwords/usernames
- Authorization = is this person allowed to do this = permissions


### Garbage collection

Can either count references and clean when count=0 (but doesn't work on circular refs), or can mark all memory locations that can be accessed in all threads and remove the remainder. GC = memory overhead, pauses program temporarily.


### Malloc

Malloc is a user space function (libc), but it makes calls to sbrk/brk to increase the size of the data segment as necessary.


Ideal project organization
-------------------------

Separate side-effects completely from any significant logic.
Raise up your data gets/writes as high as possible.
The "A-frame" architecture is a helpful way to think about this.

```
    Application/UI
    /            \
   V              V
Infrastructure   Logic
```


MVC organization
----------------

MVC (model-view-controller) is a common UI design pattern. The view contains presentational logic (often a template of some kind), the model contains business logic and persistence handling, and the controller mediates between the two.  The ideal way to design these systems with the idea of a "fat model and skinny controller". That is, the controller will contain minimal logic; it is just a basic interface that makes calls to heavier model functions. This vastily improves code reuse.

One common way to handle database access is via a DAO, or database access object. This contains all low-level database operations (CRUD, joins and others), rather than calling the database (or ORM) directly. This is useful, as it allows changing the database or ORM without drastic code changes everywhere. DAOs can complicate transactions and database query composition, but are generally a helpful concept. DAOs can be reasonably called within the controller or model, with the caveat that the controller should remain skinny (a CRUD controller function should just call the DAO directly).


Memory representation
---------------------

The byte is typically the smallest addressable unit in memory; most memory is byte-addressable. Word is the natural size with which a processor is handling data (the register size). This is typically the largest piece of data that a CPU can process at a time.

Generally speaking, a load command will bring in a whole memory line into the cache (typically > 1 word) and load that word into a register. If you only want to operate on one specific byte (e.g. a char variable), unless there are byte-specific instructions, the compiler will add in instructions to extract the byte from the word and operate on it accordingly. Data should be word-aligned (compiler handles unless overridden), since most CPUs can only read data at word boundaries (I'm assuming that this saves a bit of hardware since you don't have to worry about the last few LSBs of an address).

A page is the granularity at which the operating system converts between virtual and physical memory. Your code doesn't care what a page is; this is all handled by the OS. You start with an empty virtual address space. If you allocate some memory, the OS will give that allocated chunk of memory a starting address. If, say, your stack grows into a new page, the OS automatically allocates that page to some physical address for you. Stacks won't have fragmentation issues, since you only deallocate the memory when you are done using the memory allocated in the stack (you don't do in-place allocations/deallocations). Large pages = more internal frag, small pages = need more page table entries for 2^32 or 2^64 so larger page table.

Reasons for not using same paging system in the kernel as user processes:

- The kernel is limited to about 1GB of virtual and physical memory.
- The kernel's memory is not pageable.
- The kernel sometimes needs physically contiguous memory.
- Often, the kernel must allocate the memory without sleeping.
- Mistakes in the kernel have a much higher price than they do elsewhere.
- The kernel requests memory for data structures of varying sizes, some of which are much less than a page in size. As a result, the kernel must use memory conservatively and attempt to minimize waste due to fragmentation. This is especially important because many operating systems do not subject kernel code or data to the paging system.


Operating systems
-----------------

### Zombie/orphan processes

- `Zombie process`: A child process that terminates but is never waited on by its parent. Such a process continues to exist as an entry in the system process table even though it is no longer an actively executing program. Such situations are typically handled with a special "reaper" process that locates zombies and retrieves their exit status, allowing the operating system to then deallocate their resources.
- `Orphan process`: A child process whose parent process terminates before it does. Such situations are typically handled with a special "root" (or "init") process, which is assigned as the new parent of a process when its parent process exits. This special process detects when an orphan process terminates and then retrieves its exit status, allowing the system to deallocate the terminated child process.

### Common system calls

- Process Control (exec, fork, kill, wait)
- File management (file create, delete, open, read, set attributes)
- Device management (request/release device, read device, get attributes)
- Information (get/set time, get/set system data, get/set process/file/device data)
- Communication (open pipe, socket, signals)

### System call context switch

A system call is processed in kernel mode, which is accomplished by changing the processor execution mode to a more privileged one, but no process context switch is necessary — although a privilege context switch does occur. The hardware sees the world in terms of the execution mode according to the processor status register, and processes are an abstraction provided by the operating system. A system call does not require a context switch to another process, it is processed in the context of whichever process invoked it.

### Multiprocessing

- `fork`: new process, fork() on parent returns child, fork() on child returns zero
- `exec`: runs a binary
- `mutex`: just means mutually exclusively, but typically refers to a wait/interrupt-based/non-busy lock
- `spinlock`: needs atomic variable, polls it
- `semaphore`: producer/consumer, increments/decrements
- `barrier`
- `monitor`: set of routines in a synchronized block. Can suspend itself and wait for an event, other thread can execute in that monitor if this happens.

Typically mutex and spinlock mean "wait lock" and "spin lock", although the terminology is a bit muddy. Most modern systems today actually use hybrid mutexes and hybrid spinlocks.

When a signal is sent, it goes to the main thread on all sub-processes. Signal handlers can customize the effect of an incoming signal. Some blocked functions override incoming signals; use a timeout if blocking/sleep functions are necessary.

