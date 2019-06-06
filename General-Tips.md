# Tips-for-programming

1. 不要拒绝新的数据结构（堆、栈等），尽早接触为好
2. 注意待处理数据的范围，注意数据溢出
3. 先判断下角标 再判断其他数据。防止越界
4. 蛋疼的题处处有，蛋疼的要求满大街。。。考验耐心，学会持久战吧= =、
5. 对数据结构的值的范围要敏感！~ 用了unsigned这种之后要注意不能有负数，而且负数会循环到最大值处。。。
   （圈圈模型）
6. 压位<->位表
7. 答案明显可以分成几个部分组成的就妥妥的分成几个部分去计算吧。。。分治
8. 写程序也要分治。。。即分块，先处理干净再进入下一步骤。。。
9. DP。。。最优子结构。。。不断大事化小。。。处理每步都出现多分支、总处理步数与所输入数据量有关的问题
10. 具有对称性时可考虑逆向DP
11. 先自己模拟过程，抓住特征，再码
12. 两种类型栈共用一个下标 -> 多类型栈
13. 用笔记下各种思路
14. 预处理很重要
15. 卡用例很可能是哪里手贱写残了，或是复制粘贴漏改了
16. 坚持第2条100年不变，注意数据范围！！！
17. 分块！！！ 一个模块一个模块的写，一个模块一个模块的调
18. 任何一种成熟数据结构都有存在的理由
19. 用数组存储明显方便时，又需要避开一个一个挪（下标逐个移动），可采用静态链表
20. 尽量先做数学变换
21. 01背包相关问题可先考虑DP

## C++

1. 用`char`类型读入大数字

2. casting

   1. `4/3=1` 整数除法很危险 (implicit casting)。。。除故意，尽量用double型打头，尽量避免整数连续计算

   2. should avoid implicit casting

   3. static_cast: 

   4. reinterpret_cast: 

      1. does not compile to any CPU instructions but compile time directive

         (except for casting between `int` and pointer, or obscure architectures where pointer representation depends on its type)

      2. instruct compiler to treat `expression` as if it had the type `new_type`

      3. used to convert one pointer of another pointer of any type, no matter either the class is related to each other or not. It does NOT check if the pointer type and data pointed by the pointer is same or not.

3. `for(int i = 0, j = 0; i<m ＆＆ j<n; i++, j++)`　`，`是一种运算符，会遮盖前半句

4. lvalue (左值): can be assigned by values (ABLE to appear at the Left of "=")

5. rvalue(右值): literal constant, cannot be assigned (can ONLY appear at the Right of "=")

6.  C++ is based on class instead of on object, which means that objects of the same class can access each other's private fields without restriction

7.  `int& a = 0;` NOT okay - not able to change integer literal 

    `int const& a=0;` okay - as reference to a const)

8.  `&&` meaning: reference for rvalue

    1. move semantics: copy constructor by moving things on mem 

       $\Rightarrow$ obviate copy-destroy process for some intermediate result 

       (e.g. initialization from function return)

    2. forwarding reference in template: offer a systematic mechanism to auto-forward literal into a copy, instead of overloading function for e.g. `int&` vs. `int const&` when defining template

    3. property:

       1. when the function parameter type is of the form `T&&` where `T` is a template parameter, and the function argument is an lvalue of type `A`, the type `A&` is used for template argument deduction.
       2. For overload resolution, lvalues prefer binding to lvalue references and rvalues prefer binding to rvalue references. Hence why temporaries prefer invoking a move constructor / move assignment operator over a copy constructor / assignment operator.
       3. rvalue references will implicitly bind to rvalues and to temporaries that are the result of an implicit conversion. i.e. `float f = 0f; int&& i = f;` is okay because `float` is implicitly convertible to `int`; the reference would be to a temporary that is the result of the conversion.
       4. named rvalue references are lvalues. Unnamed rvalue references are rvalues. This is important to understand why the `std::move` call is necessary in: `foo&& r = foo(); foo f = std::move(r);`

       NOTE: https://stackoverflow.com/questions/5481539/what-does-t-double-ampersand-mean-in-c11

9. alignment trick: `union` with unused `long int` $\Rightarrow$ cpu needs to fetch mem exactly once to get the struct

10. casting type of pointer in c++

    1. `static_cast`: assume object has containing relation \& adjust the address of pointer if necessary
    2. `dynamic_cast`: check for containing relation, \& adjust address if the check passed and necessary
    3. `reinterpret_cast`: just change the type of pointer directly, no adjustment, no check
    4. `const_cast`: the ONLY casting with ability to cast away constness

    NOTE: <https://www.quora.com/How-do-you-explain-the-differences-among-static_cast-reinterpret_cast-const_cast-and-dynamic_cast-to-a-new-C++-programmer> 

11. `explicit`: only applicable to constructor, so that no implicit type casting can perform when calling it

12. `#` in macro: stringizing operator: turn `macro` into a quoted string (without expanding parameter definition)

    e.g. `#define MKSTR(s) #s`, then `MKSTR(hello)` will be replaced by `"hello"` 

13. `#@` in macro: charizing operator: make the parameter into a `char`

    e.g. `#define MKCHAR(x)  #@x` to replace `a = MKCHAR(b);` by `a = 'b';` 

14. `##` in macro: token-pasting operator: if a formal parameter in a macro definition is preceded or followed by the token-pasting operator, the formal parameter is immediately replaced by the unexpanded actual argument. Macro expansion is not performed on the argument prior to replacement.

    the tokens preceding and following it are concatenated. The resulting token must be a valid token. If it is, the token is scanned for possible replacement if it represents a macro name. 

    e.g. `#define paster( n ) printf_s( "token" #n " = %d", token##n )` 

    to make `paster( 9 );` becomes `printf_s( "token" "9" " = %d", token9 ); ` 

    then becomes `printf_s( "token9 = %d", token9 );` , output `token9 = 9` 

15. `virtual`, `override` and `final`:

    1. `virtual`: ensure the function is dynamic for derived classes; only base declaration need `virtual` to mark as virtual; superfluous for overriding or for passing virtuality to further derived class (automatically passed down)
    2. `override`: ensure the function is overriding a virtual function (a compiler check)
    3. `final` with `virtual`: ensure function is itself virtual and can NOT be further overridden by derived class; ill-formed otherwise (a compiler check + forbidding further overriding)
    4. `final` with `class`: ensure the class can NOT be a base for derived class
    5. Note: `override` and `final` is keyword only in declaration, NOT reserved in other context

### Concurrency in C++ (POSIX Library)

- Thread

  - Basic (see more in real-time note)

    - operation: creation, termination, synchronization, scheduling, interaction, data management
    - thread vs. process: thread inside process, invisible to OS (use same address space & pid)
    - shared data: OS resources (file descriptor, etc.), signals, work directory, gid, uid, pid...
    - unique data: thread id, stack pointer (control flow), local variables, signal mask, priority, error

  - Creation

    - `pthread_t` to represent thread

    - `pthread_create` to kickoff the thread (with attributes, function and args), return thread id

      note: function as pointer `void * (*start_routine)(void *)`, args as pointer `void *arg` 

    - attributes `pthread_attr_t`: 

      1. detached state:  `PTHREAD_CREATE_JOINABLE`, `PTHREAD_CREATE_DETACHED`
         1. joinable (default): holding resources (even after finished) till someone else call `join()`
         2. detached: release all resources on finish and then die. can NOT be joined by others
      2. scheduling policy: `PTHREAD_INHERIT_SCHED`,`PTHREAD_EXPLICIT_SCHED`,`SCHED_OTHER`
      3. scheduling parameter
      4. inherited schedule attribute: default: `PTHREAD_EXPLICIT_SCHED`; to inherit from parent thread: `PTHREAD_INHERIT_SCHED`
      5. scope: kernel threads-`PTHREAD_SCOPE_SYSTEM` user threads-`PTHREAD_SCOPE_PROCESS` 
      6. guard size
      7. stack address (detail in `unistd.h`, `bits/posix_opt.h`, `_POSIX_THREAD_ATTR_STACKADDR` 
      8. stack size: default: minimum `PTHREAD_STACK_SIZE` (set in `pthread.h` 
      9. initialize to default: `pthread_attr_init`; destroy by `pthread_attr_destroy` 

  - Termination

    - `pthread_exit` to kill the current thread \& return (can also be achieved by `return`)

    - `cancel` to signal other thread to cancel, if it is cancelable, or block till it becomes cancelable

      (preemption control, atomic get-set of cancelability \& cancel type w.r.t other threads)

  - Synchronization

    - mutexes: semaphore

    - joins: wait till others termination 

    - conditional variable `pthread_cond_t`

      1. variable invoking is implemented by signaling $\Rightarrow$ need to wait before others signal

         (otherwise miss the signal !)

      2. no compiler check for library $\Rightarrow$ need a syntactically unrelated semaphore to help :(

         (though the waiting & signaling of the semaphore encapsulated in `phread_cond_wait`)

         (the same lock is used to enter the critical region \& wait on conditional var - as itself is shamelessly not mutual exclusive)

      3. need a loop to go sleep again if invoked and failed in trying to grab the lock

         (potential live lock as conditional critical region)

         (shitty POSIX standard: invoke **at least** one thread)

      $\Rightarrow$ not even a conditional critical region...

      example code for waiting:

      ```c++
      while (cond is true) { 				// re-sleep if failed to evaluate to true
      	pthread_cond_wait(&cond, &mtx); // sleep in the queue & release the lock
      	// invoked, but not sure if is me
      }
      // lock mtx acquired at this point - it is me who inside critical region now
      ```

-  `futex` Fast User Level Locking

  - Paper

    - https://www.kernel.org/doc/ols/2002/ols2002-pages-479-495.pdf

  - Design Goal

    - avoid system calls if possible, as system calls typically consume several hundred instructions.

      1. uncontended case: the application changes the lock status word without into the kernel

         $\Rightarrow$ atomic operation required at user level

      2. contended case: require waiting queue \& scheduling 

         $\Rightarrow$ into the kernel; kernel object needed, yet not always needed $\Rightarrow$ allocate on demand (compare to prior allocation e.g. IPC mechanisms)

    - avoid unnecessary context switches: context switches lead to overhead associated with TLB invalidations etc.

    - provide foundation for complicated synchronization, e.g. semaphores, rwlocks, blocking locks, or spin versions of these

    - provide solution to recover from “dead” locks.

  - Limitation

    - hard to determine lock ownership: lock acquired by manipulating use-space memory

  - Lock in Use Space

    - ability of virtual address to represent dynamic allocated lock
      1. anonymous memory: inside only single process
      2. shared memory segment: over multiple processes (mostly used)
      3. memory mapped files: over multiple processes
    - virtual address as kernel handle
      1. able to be recognized to refer to the same underlying kernel object (if any)

  - Implementation 

    - unique identifier for each futex (may have different virtual addresses in different process)

      1. identifier: a pointer to “struct page”  and the offset within that page
      2. incrementing the reference count to avoid being swapped out (while process sleeping) 

    - hash table to indicate process and the futex it is waiting on, created on process kernel stack

      (as now contend happened)

    - basically, writing assembly in user code that utilize machine-level atomic operation

      (further detail in "linux/kernel/futex.c")

  - Procedure (the Basic Implementation)

    - check user address alignment; pin the page (increase reference count to not be swapped out)

    - add the address (page ptr with offset) hashed into the table, becoming a hash bucket

    - FUTEX_DOWN op (acquiring / potentially waiting the lock)

      1. process marked INTERRUPTIBLE - ready to stop

      2. futex acquired (denoted by page+offset) added to the tail of hash bucket determined above 

         (to the tail so as to have FIFO ordering for wakeups)

      3. attempt an atomic decrement of the futex address 

         (not attempt if it is already negative, to avoid wrap around)

      4. if futex becomes 0, lock acquired, process marked as RUNNING, remove our futex from hash table, wake next process (if any) to decrease its futex to -1 to indicate it is waiting

      5. if lock busy, my futex becomes -1, a signal received, go into kernel and suspend myself 

    - FUTEX_UP (releasing the lock)

      1. increase my futex by 1 (set to 1)
      2. found the hash table bucket for my futex and wake up the first process (at the head)

    - unpin the page (able to be swapped out if needed)

    - cons:

      1. timeout: NOT easy to protect associated timer from being used by other purposed
      2. starvation: if the thread releasing the lock acquire it immediately again, it will win the contend
      3. live-lock not considered

  - More Flexible Implementation

    - FUTEX_WAIT: check if lock (a number) == the number passed in, to find out if contending; and then decide proceed or suspend itself in kernel

      (able to specify time-out, with the help of kernel)

    -  FUTEX_WAKE: not altering the lock, but just wake process waiting on the futex lock if any (with kernel help)

  - Note

    - nowadays `mutex` ARE based on `futex` 
    - `futex` nowadays becomes a wait queue technique accessible in the user space

## Ada

1. Only operator `=` can be promoted (to perform concurrency)
2. `for all i in range` => suggest concurrent performance.
3. modular type can NOT be runtime dependent.
4. can NOT have variant record in array. => compiler refuse to alloct heap mem at runtime
    => but CAN use mutable variant record (where a default value is given)
    ​	=> but can vary in the fly.
5. Make sure sub-task get the termination signal before main task terminated by either
    1. terminating safely, or 
    2. raising an error
## Python

1. @：numpy 矩阵乘法 => can have difference between *,@,multiply,dot (for matrix/ndarray)
2. explicit matrix inverse computations should be avoided for reasons of numerical stability
    => 用 numpy.linalg.solve()
3. matrix[:,0:1] => 矩阵第一列
    => 前闭后开
4. X = PolynomialFeatures(degree=2).fit_transform(X) => X每个features 的 2次多项式展开
5. np.random.shuffle(idx) => shuffle (index of) data set


7. 式子中改变+/- 号位置会影响精确度 => 调用opt.fmin_bfgs() 中有warning

8. U,S,V = np.linalg.svd(A)
     =>	S is 1-D array with corresponding number
       	V is matrix with row vectors being eigenvectors of A.T*A （U==V.T when A = X.T*X）

9. OptionParser to accept comman-line parameters

10. gc to release memory explicitly (gc.collect()) $\Rightarrow$ gc short for garbage collection

11. place the outer python-based library directly under the  folder => then import as usuall

12. MatPlotLib


      1. API Overview
    
           1. `matplotlib.backend_bases.FigureCanvas` : the area onto which the figure is drawn, 
           2. `matplotlib.backend_bases.Renderer` : object knowing how to draw on `FigureCanvas``
           3. `matplotlib.artist.Artist` : object knowing how to use a renderer to paint onto the canvas
    
      $\Rightarrow$ `FigureCanvas` and `Renderer`  handle all the details of talking to UI toolkits
    
      $\Rightarrow$ `Artist` handles high level constructs e.g. representing and laying out the figure, text, and lines

13. In fact, **EVERYTHING** is object in python

14. function decorator: special syntax sugar to manipulate the result of a function


      1. foundation


           1. function can be assigned into a variable (related to lazy evaluation)
           2. concept of scope: read-only access to the parent scope
           3. easily passing arbitrary arguments by `*args`, `**kwargs` 
    
      2. define a decorator: define a function to manipulate another function
    
           (build upon given function)
    
      3. decorate a function: `func = decorator(func)` 
    
      4. put `@decorator` above the definition of function to be decorated
    
           $\Rightarrow$ python interpreter helps decorate automatically! 
    
           $\Rightarrow$ easily stack decorators into a pipeline (to decorate a function)
    
      5. retain the attributes of original function


           1. get overwritten by wrapper (e.g. `__doc__`, `__name__` and `__module__`)
    
           2. `from functools import wraps`: `@wraps(func)` before self-defined wrapping behaviour
    
                $\Rightarrow$ copy all attributes of original function to the one returned by the wrapper
    
      6. ability of decorator


           1. pre-/post- condition, synchronization, redirect std ouput, async call, etc....
           2. decorator library: https://wiki.python.org/moin/PythonDecoratorLibrary

### Inheritance

- Class Generation
  - Meta Class: 
    - class is **object** in python $\Rightarrow$ can be generated on the fly, or, by a meta class - dynamically
    - $\Rightarrow$ see https://stackoverflow.com/questions/100003/what-are-metaclasses-in-python
  - `__init__`
    - handler for folder importing - better to use relative import
- Multi Inheritance
  - Method Resolution Order (MRO)
    - maintain the precedence appeared in the inheritance list in the derived class
    - super() call:
    - find & gather the next class in MRO list into a stack
    - init & pop the class from that stack $\Rightarrow$ last in first out
  - Passing Parameters
    - bass class need to be designed for multi-inheritance
    - $\Rightarrow$ intermediate class need to take extra parameters to pass along the MRO
    - $\Rightarrow$ force designer to be aware of the MRO precedence!

### Concurrency in Python

- Generator Functions

  - Description

    - `yield` susppend function until `next()` called
    - can be automatically wrapped into a generator if not embedded in a `for` loop
    - `return` raise `StopIteration`, together with value propagated

  - Generators Pipeline

    - data $\rightarrow$ generator $\rightarrow$ generator $\rightarrow$ ... $\rightarrow$ `for` x `in` s: final processing

      $\Rightarrow$ pull data items through pipe (from the last generator)

    - $\Rightarrow$ need to embed with the data producer

      (the processing between the producer and the data user)

    - can generalize to many-to-1 dataflow (collect data from multiple generators)

- Coroutine

  - Creation

    - include `yield` as `x = (yeild val)` in a generator function

  - Primming

    - call `next()` - equivalent to `.send(None)`, before sending any not `None` data
    - $\Rightarrow$ advance the execution to the `= (yield val)` location 
      1. have val `yield` to parent scope
      2. have itseld suspended
      3. $\Rightarrow$ ready to receive data
    - usually use decorator to do the first `next()` & better readability

  - Description

    - accept `.send(data)` call at `x = (yeild)` location 

      $\Rightarrow$ `x = data`, where `data` is sent from outside

    - generator proceed until next `yield`

  - Termination

    - generator not knowing the end by itself ! (as data sent from outside)

    - $\Rightarrow$ `.close()` to shut down

      note: grabage collection will call `.close()` as well

    - clean-up routine: catching `.close()` inside coroutine with the raised `GeneratorClose` 

  - Raise inside Coroutine

    - `.thorw(exception, exception_info)` to trigger the given exception 

      $\Rightarrow$ at the location `x=(yeild)` 

    - $\Rightarrow$ needs to have good exception handler! (as otherwise propagated back)

  - with Generator

    - can become a window into the generator 

      $\Rightarrow$ conditionally triggered ` = (yeild)` to tweak the state of generator

      (discouraged though)

    - separate view (encouraged)

      1. generator produces data in series
      2. coroutine consumes data in series

  - Coroutine Pipeline

    - data $\rightarrow$ coroutine.send() $\rightarrow$ ... $\rightarrow$ final product

      $\Rightarrow$ push data items through the pipe (push through each coroutine)

    - decoupled with data producer

      $\Rightarrow$ producer becomes an active role: need to push data into pipe

    - vs. generator pipeline

      1. push data
      2. can decouple from data producer
      3. can generalize to 1-to-many dataflow (multicast data to coroutines )

    - vs. pure function: 

      1. coroutine can maintain a state

         $\Rightarrow$ series processing (as function cannot stop in the mid of processing)

         $\Rightarrow$ save memory / earger execution (as downstream get data before all upstream finished)

         $\Rightarrow$ ready to transformed into true pipeline (each part in parallel)

      2. can internally couple with final data user (which is also a coroutine)

         (call `.send()`of next coroutine from the current coroutine - painful setup required though)

      3. can use a coroutine as data source to start pushing

    - vs. class

      1. state maintenance with little consumption

         $\Rightarrow$ no `self` lookup / object instantiation / library import

         $\Rightarrow$ faster & lighter

  - General Ability

    - event handling - as capable of receiving & sending
    - $\Rightarrow$ passive state machine - as able to store state
    - initial data source can be low-level API - as long as `.send()` invoked eventually
    - ready for **parallel & distributed** system $\Rightarrow$ send/receive via message / shared mem

- `yeild`

  - Communication

    - funtion $\rightarrow$ parent scope: as generator, pass out data via `yeild data`

    - parent scope $\rightarrow$ function: as coroutine, pass in data via `data = (yeild)` with `.send()`

    - `received_data = yeild data`: inner function receive & pass out data using single suspence

      $\Rightarrow$ pass out data to parent scope; suspend till result sent from parent scope

  - Function Suspence 

    - `next`/`.send()`/embed in a `for` loop: function executes till next `yeild` 

  - Subroutine Yielding

    - only `yield` to its parent (pass the control to parent scope)

    - `yield subroutine` pass a coroutine/generator object to its parent scope

      (instead of execute the subroutine till its yield/return)

    - $\Rightarrow$ `yield from`: dig into `yield/return` of subroutine before yield to parent

      1. `.send` to current function would passed into subroutine

      2. value `yield` to the parent scope is from subroutine

      3. exception from subroutine propagated directly into parent scope

         except for `StopIteration`: transferred into a normal `yield val` to parent scope

      $\Rightarrow$ current function becomes transparent between its parent and subroutine

  - Maintainability

    - iteration: a producer of data
    - receiving messages: a consumer
    - trap / signal: cooperative multitasking

    $\Rightarrow$ select only SINGLE role

- Concurrency

  - Global Interpreter Lock

    - purpose

      1. garbage collection needs a universal view of reference $\Rightarrow$ reference count
      2. need synchronization when counting the reference
      3. $\Rightarrow$ set a lock for the whole interpreter to protect this global count

    - implementation

      1. cpython interpreter: compatible to C API, thus GIL

      2. ironpython, javapython and other interpreter implemented with other language

         $\Rightarrow$ no GIL!, but locks at various granularity

  - Coroutine in Thread

    - coroutine + `Thread.start()` at proper point (before `=(yield)`)

    - message queue via `Queue()`, data transfer wrapped by `msg_queue.get()/put()` 

    - separate implementation (coroutine) & runtime (wrapping threads / process...)

    - overhead introduced: 

      1. msg communication
      2. thread setup

    - potential problem

      1. `.send()` needs synchronization - sender's one-time try, success or error

         $\Rightarrow$ `send` NOT designed as blocking call for sender $\Rightarrow$ receiver should wait in rendezvous

    - concurrency analysis

      1. thread concurrency achieved (actually at control-flow level)

         $\Rightarrow$ no scheduler nor any runtime environment guarantee but active `yield` 

      2. can be parallel from OS view: OS running syscalls while other python control flow running

  - Coroutine are Task (fake concurrency due to GIL - control flow level concurrency)

    - the essence of task (in python)

      1. stand-alone control flow $\rightarrow$ as a function

         1. internal state $\rightarrow$ as local variable

      2. schedule ability - can suspend/resume

         $\rightarrow$ suspend on `yeild`; resume on `send` being called; terminate on `close`

         (but, NOT recognized by OS, yet much cleaner than `fork` from the program view)

         (only active suspend within the task, task can always grap python Global Interpreter Lock)

      3. communication with other tasks $\rightarrow$ `(yeild)` receive data & `send` send data to others

      4. critical region control

         $\rightarrow$ because of Global Interpreter Lock, only one control flow can be awake at a time...

    - multitasking with coroutine only

      1. `yield` as soft interrupt (signal) to invoke its parent

         $\Rightarrow$ request OS service - syscalls & receive the result

      2. parent scope as OS, handling the `yeild` request & exception (on termination or other) 

    - achievement: 

      1. concurrency at control-flow level
      2. can be parallel from OS view: OS running syscalls while other python control flow running

  - Potential Problems

    - real OS may suspend the python preocess (interpreter) for real OS request (syscalls)
    - control-flow-level concurrency $\Rightarrow$ GIL gives knowledge of "signle task alive at a time"

  - Concurrency Analysis

    - concurrency at the level of control flow

- `select` Module in Python

  - Controlable Blocking Call
    - wait on real OS resource for a specified timeout

- `threading` Module

  - Control Flow Swicthing in Python
    - indeed, a wrapper over coroutine...using class as shown above

- `multiprocessing ` Module

  - Local Multiprocessing
    - wrapping over function (function - program for subprocess, passed as parameter)
    - `Pool(n)` to spawn $n$ subprocess, `subprocess.map()` to pass in function with parameters
    - communication & shared resource: `Queue`, `Pipe`
    - resource control: `Manager`

  - Multiprocessing over Netwrok
    - shared mem over network e.g. Redis (database on mem) 
    - listener for change in shared mem: can be maintained by library e.g. RQ, Celery, ...

  - Creating Processes
    - spawn
      1. only necessary resources copied into child process $\Rightarrow$ slower
      2. provided with semaphore tracker

    - fork
      1. call the `os.fork()` - c fork
      2. safely forking multi-threaded process is problematic

    - forkserver

      1. a server (single-threaded) process to create new process (fork itself)
      2. provided with semaphore tracker

    - semaphore tracker

      1. unlinking named semaphores - limited number & not auto-collected till next reboot

    - context

      1. $\Rightarrow$ select in default context by `mp.set_start_method('spawn')` 

         (singel start method choice allowed in one context)

      2. use `get_ context` for separate environment of multiprocessing

    - `Pool`

      1. start a group of worker to take arbitrary control flow (function)s
      2. recommend to use `with` block for neat clean-up

  - Communication between Process

    - `Queue` - shared mem 
      1. python thread and process safe
      2. not overall safe - not prevented killing by OS while in the critical region
    - `Pipe` - dual message passing
      1. duplex connection between a pair of processes
      2. not process / thread safe on single end (send/recv) $\Rightarrow$ keep it private
    - shared mem - `Value`, `Array` 
      1. process and thread-safe
    - server processs - `Manager` 
      1. server for passing objects $\Rightarrow$ more flexible, able to cross network, yet slower

  - Synchronization

    - lock - semaphore level

  - Overhead

    - memory of program copied into each subprocess
    - multiple python interpreter (if GIL)

- Multiprocessing with TF

  - TF Behaviour

    - it is running on its own, having the whole python process

    - it is asynchronous inside, already in multiprocessing

      (while, python multiprocessing is based on `fork`)

    - $\Rightarrow$ python multiprocessing with multiple tf imported will break the synchronization point in TF

    - $\Rightarrow$ NOT really able for hand-crafted multiprocessing  

  - Work-around

    - import TF in scope $\Rightarrow$ provide the inner-tf multiprocessing with scope
    - $\Rightarrow$ import TF inside each function call (scope), with NO process-level TF running

## Octave / Matlab

1. unrolling parameters: deltaVector = [ D1(:) ; D2(:) ] // matrix D1,D2 $\Rightarrow$ column vector 
2. Reshape: D1 = reshape (deltaVector(1, m\*n), m, n) // column vector $\Rightarrow$ m\*n matrix

## Data

1. h5py is a good thing: light-weight dataset structure
2. TF.Data: a pipeline for fetching, parsing and feeding the data (in a separate thread $\Rightarrow$ parallel)

### Potobuf

- reading protobuf byte stream from python client

  - for message from python client: `rst.ParseFromString(ptxt_bt)`

  - for message from other client: `protobuf.text_format.Merge(ptxt_bt, rst)` 

    ```python
    try:
        protobuf.text_format.Merge(ptxt_bt, rst)  # read from non-python
    except:
        rst.ParseFromString(ptxt_bt)  # read from python
    ```

- as Configuration File

  - `ParseFromString`: read from a `string` type (instead of `byte` as above)
  - File Structure
    - just write configuration as coding style...
    - concrete style can be checked by printing the class after reading a proto files

## QT

1. Event 

   - Registration

     - in the code, widget overrides the event handler in its class

   - Routine

     - main thread (app.exec_) waked up by system & read from the buffer & create an event
     - locates the corresponding widget through the event handlers UI chain (down propagation)
     - executes the defined handler & terminate / further propagate the event

   - Cons

     - every widget with customized behavior expects a class implementation

       note: even of the same type (e.g. button)

2. Signal-Slot

   - Registration
     - in the code, define slot function (handler for signal)
     - in the code, widget connects desired signal to slot function (many-to-many)
     - at compile time, QT core register slot functions to a list of signal
     - at runtime, each slot function gets attached a queue 
   - Routine
     - main thread (app.exec_) waked up by system & read from the buffer & create a signal
     - find out all registered functions from the list
     - push the signal to their queue
     - functions (potentially sub-threads) wait on queues & execute
   - Pros
     - more parallel
     - dynamic dispatch at function level

## Research Lib TO DO List

1. glob.glob lib to replace file_traverser class
2. 