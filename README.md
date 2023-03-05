# Starve_free_readers_wriers_problem_solution
The problem is to synchronize the access of a data structure by readers and writers without anyone being starved indefinitely.
The following is the pseudo code for my solution to the starve free readers-writers problem. 

wait(s) means the process decrements s and if s is <0 it waits in blocked queue till any other process increments s by signal(s) and this is first process in the queue
signal(s) increments s and if s is <=0 it wakes first process in the blocked queue of s if any.

### My approach to solve the problem
we have seen readers dominant solution in which writers doesn't get a chance until all the readers are done reading. We can solve this problem by keeping another variable for the numbers of writers waiting and a entry semaphore. The entry semaphore is for both readers and writers readers wait for this semaphore update in_read_count(number of readers reading at the moment) and signal entry semaphore and enters critical section(reads the data). but writers wait for entry and once they get it the release it only after writing is done(it ensures that till reading is completed no other reader can increment in_read_count,i.e., cannot enter critical section and same to the wriers). The writer with entry semaphore waits till the readers already inside critcal section to complete their job (wait(wrt)) and once done the last reader signals wrt and hence the witer starts writing after completion he signals entry.

# Implementation of the above solution is as follows
### semaphores and variables intialization

semaphore entry=1;
semaphore m=1;
semaphore wrt=0;
int in_read_count=0;
int wrt_waiting=0;

### readers implementation

```c++
wait(entry);
 wait(m);
  in_readcount++;
 signal(m);
signal(entry);
/*read*/
wait(m);
 in_read_count--;
 if(in_read_count==0){
  if(wrtwaiting){
    signal(wrt);
  }
 }
 signal(m);
 ```
 
 ### writers implementation
 ```c++
 wait(entry);
  wrtwaiting=1;
  if(in_read_count){
   wait(wrt);
  }
  /*write*/
  wrtwaiting=0;
 signal(entry);
 ```
 
 ## Corectness of the solution
 
 ### Mutual exclusion
 we have ensured that a writer writes only when no reader is in the critical section by waiting for wrt(waiting for all the previous readers to finish reading),and
 not allowing any other readers into C.S by keeping entry semaphore with the writer. and any number of readers can access C.S if no writer is writing. Hence This solution ensures mutual exclusion.
 ### Progress
 We have ensured there occurs no deadlock and also entry of any process into C.S is not decided in the remainder section ,Hence this solution satisfies progress critirea also.
 ### Bounded waiting 
 Here our writer just have to wait for the readers that came before him and he can access C.S after all readers and writers that came before him completes which takes finite time hence there no unbounded waiting for writers in this solution as in readers dominant solution, readers just have to wait for the writers that came before them finishes their job and once done he can access C.S. Hence no unbounded waiting for both readers and writers.Hence there is no starvation for both readers and writers.
 
