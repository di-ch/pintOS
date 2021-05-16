---- GROUP ----

group number 4

Dmitriy Cheremukhin sonya_vst@gm.gist.ac.kr

Sofia Vystoropets dicher@gm.gist.ac.kr

---- PRELIMINARIES ----

The objective of the project is to implement priority scheduling and priority donation.

Whenever a thread is created or unblocked and is put into a ready queue, this queue must be reordered by the priorities of all the threads in it. Also there must be priority donation when the current thread needs to acquire the lock and the lock holder can not run due to its lesser priority.

---- DATA STRUCTURES ----

File thread.h.

int orig_priority // The priority of the thread at creation

void thread_priority_donate(struct thread *, int priority) // The function that allows priority donation

File synch.c

int priority // The priority of the thread holding the lock

---- ALGORITHMS ----

File thread.c

Function thread_priority_donate() allows to donate priority from one thread to another

Added scheduling in the thread_unblock() functio. After its unblocking the thread will be put into the ready list sorted by the priority of all the threads in it. If the unblocked thread has lesser priority than any other it will yield the CPU.

Function thread_set_priority() was changed to implement priority donation.

Added helper function to compare threads priorities.

File synch.c

Function sema_down() was changed to order the sleep list after blocking the thread.

Function sema_up() was changed to choose the thread with the highest priority from the sleep list.

Added priority donation to the function lock_acquire() and also implemented ordering of the locks held by one thread by priority.

Function list_sort() was changed to restore the priority donation.

Changed the function cond_wait() for the threads waiting for condition to be ordered by their priorities.

Implemented three helper functions to compare threads, locks and semaphores priorities.

---- RATIONALE ----

This code is rather simplistic and effective because it implements both the priority scheduling and priority donation. 

Helper function helps to implement priority donation just by comparing priorities of lock holder and the running thread.
