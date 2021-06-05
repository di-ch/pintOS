---- GROUP ----

group number 4

Dmitriy Cheremukhin sonya_vst@gm.gist.ac.kr

Sofia Vystoropets dicher@gm.gist.ac.kr

---- PRELIMINARIES ----

The objective of the project is to enable programs to interact with the OS via system calls. First problem is to make system print the terminating process. Second problem is to implement passing of arguments to new processes. The last problem is to implement the system calls handler.

---- DATA STRUCTURES ----

File syscall.c

static void syscall_handler // The handler of system calls. Enables several functions with system calls: executing, exiting, waiting, creating, removing.

File process.h

struct process_control_block // Creating pcb structure to define the running process.

---- ALGORITHMS ----

File process.c

Changed process_execute function to create a pcb along with file_name and pass it into thread_create so that a newly created thread can hold the PCB of the process to be executed.

Changed start_process function to assign pcb and one-to-one map pid and tid.

---- RATIONALE ----

This code implements needed functions for the problems listed in the main task. However it cannot run due to the error with accessing another file.
