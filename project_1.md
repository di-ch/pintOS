

---- GROUP ----

group number 4

Dmitriy Cheremukhin <sonya_vst@gm.gist.ac.kr>

Sofia Vystoropets <dicher@gm.gist.ac.kr>

---- PRELIMINARIES ----

None.

---- DATA STRUCTURES ----

File thread.h.

Added to struct thread:

int orig_priority;
struct list_elem sleepelem;
int64_t sleepend_tick;

struct lock *wait_lock;
struct list locks;
void thread_sleep_until (int64_t wake_tick);
void thread_priority_donate(struct thread *, int priority);

File synch.h.

Added to struct semaphore:

struct list_elem lockelem;
int priority;

---- ALGORITHMS ----

File thread.c.

Function thread_awake() wakes up all the threads with expired
time in the sleeping state on every timer tick. It puts the needed
threads to the ready queue. This function requires turning off the interrupts.

Function thread_sleep_until() blocks the thread until the
waking tick that is given to this function as an argument.
This function is needed to avoid busy wait in timer.

Function thread_priority_donate() allows to donate priority
from one thread to another. It will be used in the synch.c
file when the lock holder has lesser priority than the
current thread.

Added sleep_list in the thread_init() to initialize the
list of threads in a sleeping state.

Changed the argument for the thread_tick() function to the
int64_t tick. Thus, this function will awake threads that
have exceeded the amount of time in sleeping state.

Added the thread_unblock() function to thread_create(). This
will put the created thread to the ready queue. Also added the
implementation of context switching for the advanced scheduler.
Created thread will be checked for its priority and if it is
not high enough, the thread will yield the CPU.

Added scheduling in the thread_unblock() function. After its unblocking,
the thread will be put in the ready list sorted by the priority of
the threads in it. If the unblocked thread has lesser priority than
any other it will yield the cpu.

Added implementation of releasing all the locks coupled with
the thread in thread_exit(). It is done because after the thread
destroying, all its locks must be acquired by other threads.

In the thread_yield() function after the thread yields the cpu
it must be put into the ready queue by the scheduler. Added the
ordering of the ready list by priority.

Function thread_set_priority() was changed to implement priority donation.
If the priority donation takes place than this function only
changes the current priority.

Added helper function to compare threads priorities.

---- SYNCHRONIZATION ----

---- RATIONALE ----
