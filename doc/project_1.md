

---- GROUP ----

group number 4

Dmitriy Cheremukhin <sonya_vst@gm.gist.ac.kr>

Sofia Vystoropets <dicher@gm.gist.ac.kr>

---- PRELIMINARIES ----

This project consists of three objectives: changing the alarm
clock implementation, implementing priority scheduling and
creating advanced scheduler. 

In first part the problem is with the function timer_sleep()
makes the thread yield the cpu after going to the sleeping state.
The function thread_yield() puts the thread in ready queue even
if the thread does not have anything to do at the current time
and this busy waiting wastes cpu resourses. The solution is to 
create a list of sleeping threads to put the blocked thread into and
implement a function that will wake up the thread and put it in the 
ready queue when the sleeping time is over.

In the second part the problem is to make a priority scheduling
that is not implemented in the original pintOS. That means that
whenever a thread is created or unblocked or awaken and put in
the ready queue, this queue must be reordered by the priorities
of all the threads in it. Also there must be the priority donation
when the current thread needs to acquire the lock and the lock
holder can not run due to its lesser priority.

The third part is about creating a scheduler that will allow
to order several queues of threads by their priorities that will
enhance the efficiency of cpu resourses distribution.

---- DATA STRUCTURES ----

File thread.h.

Added to struct thread:

int orig_priority;                //The priority of the thread at creation
struct list_elem sleepelem;       //List element, stored in the sleep queue
int64_t sleepend_tick;            //The tick after which the thread should awake

struct lock *wait_lock;           //The lock upon which the thread waits
struct list locks;                //List of the current threads locks
void thread_sleep_until (int64_t wake_tick); - the function that blocks 
the thread until it must be awaken
void thread_priority_donate(struct thread *, int priority); - the function 
that allows priority donation

File synch.h.

Added to struct semaphore:

struct list_elem lockelem;         //List element for the threads locks
int priority;                      //The priority of the thread holding the lock

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

File synch.c.

Function sema_down() was changed to order the sleep list after 
blocking the thread.

Function sema_up() was changed to choose the thread with the highest
priority from the sleep list.

Added priority donation to the function lock_acquire() and also
implemented ordering of the locks held by one thread by priority.

Function list_sort() was changed to restore the priority donation.

Changed the function cond_wait() for the threads waiting for
condition to be ordered by their priorities.

Implemented three helper functions that compare threads, locks
and semaphores priorities.

File timer.c.

Reimplemented the function timer_sleep(). Now after blocking
the thread it is put to the sleep list and stays there until
the needed number of ticks passes.
---- SYNCHRONIZATION ----

Every thread is now ordered in the queues by its priority. After 
creating it is put to the ready queue where it can be switched
immediately with the running thread if it has higher priority, and
if it does not, than the ready list is reordered. When the thread
has done its work, it goes to sleep queue where it waits for
the timer ticks to pass. When it is unblocked the thread again is
put to the ready queue due to its priority.

Same principle occurs with the locks. Threads are waiting to
acquire the lock in waiting queue that is ordered by their 
priorities. The list of locks held by one holder is ordered
by the locks priorities. When the lock is done working it 
is released and the locks list is rescheduled, when the thread
is done working it releases all its locks. If the thread needs
the lock but it is held by the thread with a lesser priority,
than the priority donation occurs.

---- RATIONALE ----

This code is rather simplistic and effective, because it
reimplements the threads sleeping and makes priorities
scheduling available. Making a queue that consists of
blocked threads makes the advantage in cpu resourses 
distribution.

However, this code does not take into account possible
deadlines of the threads so it cannot be used in the 
systems with time stamps for the processes. 
