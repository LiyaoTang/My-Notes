# Tips-for-programming

1. 用char类型读入大数字
2. 4/3=1 整数除法很危险。。。除故意，尽量用double型打头，尽量避免整数连续计算
3. for(int i = 0, j = 0; i<m ＆＆ j<n; i++, j++)　“，”是一种运算符，会遮盖前半句
4. 不要拒绝新的数据结构（堆、栈等），尽早接触为好
5. 注意待处理数据的范围，注意数据溢出
6. 先判断下角标 再判断其他数据。防止越界
7. 蛋疼的题处处有，蛋疼的要求满大街。。。考验耐心，学会持久战吧= =、
8. 对数据结构的值的范围要敏感！~ 用了unsigned这种之后要注意不能有负数，而且负数会循环到最大值处。。。
   （圈圈模型）
9. 压位<->位表
10. 答案明显可以分成几个部分组成的就妥妥的分成几个部分去计算吧。。。分治
11. 写程序也要分治。。。即分块，先处理干净再进入下一步骤。。。
12. DP。。。最优子结构。。。不断大事化小。。。处理每步都出现多分支、总处理步数与所输入数据量有关的问题
13. 具有对称性时可考虑逆向DP
14. 先自己模拟过程，抓住特征，再码
15. 两种类型栈共用一个下标 -> 多类型栈
16. 用笔记下各种思路
17. 预处理很重要
18. 卡用例很可能是哪里手贱写残了，或是复制粘贴漏改了
19. 坚持第5条100年不变，注意数据范围！！！
20. 分块！！！ 一个模块一个模块的写，一个模块一个模块的调
21. 任何一种成熟数据结构都有存在的理由
22. 用数组存储明显方便时，又需要避开一个一个挪（下标逐个移动），可采用静态链表
23. 尽量先做数学变换
24. 01背包相关问题可先考虑DP

## Ada

1. Only operator "=" can be promoted.
2. for all i in range => suggest concurrent performance.
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

  - Coroutine in Thread (fake concurrency due to GIL)

    - coroutine + `Thread.start()` at proper point (before `=(yield)`)

    - message queue via `Queue()`, data transfer wrapped by `msg_queue.get()/put()` 

    - separate implementation (coroutine) & runtime (wrapping threads / process...)

    - overhead introduced: 

      1. msg communication
      2. thread setup

    - potential problem

      1. `.send()` needs synchronization - sender's one-time try, success or error

         $\Rightarrow$ `send` NOT designed as blocking call for sender $\Rightarrow$ receiver should wait in rendezvous

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

      1. control flow switching within single process
      2. 

  - Potential Problems

    - real OS may suspend the python preocess (interpreter) for real OS request (syscalls)
    - control-flow-level concurrency $\Rightarrow$ GIL gives knowledge of "signle task alive at a time"

- `select` Module in Python

  - Controlable Blocking Call
    - wait on real OS resource for a specified timeout

## Octave / Matlab

1. unrolling parameters: deltaVector = [ D1(:) ; D2(:) ] // matrix D1,D2 $\Rightarrow$ column vector 
2. Reshape: D1 = reshape (deltaVector(1, m\*n), m, n) // column vector $\Rightarrow$ m\*n matrix

## Data

1. h5py is a good thing: light-weight dataset structure
2. TF.Data: a pipeline for fetching, parsing and feeding the data (in a separate thread $\Rightarrow$ parallel)

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