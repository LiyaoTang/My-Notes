# General Tips

## C++

### Usual Usage

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

6. C++ is based on class instead of on object, which means that objects of the same class can access each other's private fields without restriction

7. `int& a = 0;` NOT okay - not able to change integer literal 

   `int const& a=0;` okay - as reference to a const)

8. `&&` meaning: reference for rvalue

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

16. `class`  forward declaration

    1. to reduce coupling between header files

       $\Rightarrow$ no need to re-compile all otherwise cascaded headers

       (hence faster compilation after modification) 

    2. can use only pointer $\Rightarrow$ cannot include instance 

       (cannot instantiate as no definition provided)

17. return a reference

    1. note canNOT return a constant then (e.g. nullptr NOT allowed)
    2. => NO need to return reference of a pointer

18. vector::push

    1. implies a "copy construction" $\Rightarrow$ safe to use local constructed variables

19. protobuf

    1. nested structure: 
       1. `mutable_xxx()` to get an allocated object (auto delete previous allocated mem)
       2. `set_allocated_xxx()` to point the pointer (inside proto class) to the allocated mem
       
    2. understanding
    
       1. a standard way to define \& create classes 
    
          $\Rightarrow$ use complier to check if all necessary component defined
    
          1. avoid human mistake
          2. consistent interface
          3. human friendly \& explanable by itself
    
       2. class generator
    
          1. easier to define general object: create setter \& getter automatically
    
20. function object

    1. override `()` operator $\Rightarrow$ class instance has behaviour similar to function

21. `lambda` function

    1. syntax: `auto lambda_func = [var] (T1 var1) -> T2 {return ...}` 

       full syntax: `[ capture-list ] ( params ) mutable(optional) constexpr(optional)(c++17) exception attribute -> ret { body } `

    2. meaning:

       1. `-> T2` to specify return type, can be omitted \& rely on type deduction
       2. `[x]` to capture local accessible variable $\Rightarrow$ to be stored \& used in the lambda

    3. `[]` capture

       1. create a function class : a class with `()` operator overridden $\Rightarrow$ callable object

       2. `[x]`: copy the local variable `x` $\Rightarrow$ class equipped with non-static member

       3. `mutable`: create stateful lambda, by enabling modification 

          1. `auto lambda_func = [var] (T1 var1) mutable {var += 1; ...}` 

          2. by creating a non-const `()` override

          3. `[&x]`: create reference to local variable `x` $\Rightarrow$ can be always modified

             (no need for explicit mutable)

             $\Rightarrow$ can be controlled outside the lambda **!** 

       4. type of capturing variable

          1. `[]`: no variable
          2. `[=]`: copy all accessible local variable by value (copy) 
          3. `[x]`: capture only `x` by copy
          4. `[&x]`: capture only `x` by reference
          5. `[=, &x]`: capture all var by copy, but `x` by reference
          6. `[&, x]`: capture all var by reference, but `x` by copy
          7. `[this]`: capture current object instance (by pointer)
          8. `[*this]`: a copy of current object instance (by value)

       5. danger

          1. capture by reference: dangerous for dangling reference, i.e. local variable released 

             (out-of-scope)

          2. capture all var by copy: dangerous for implicitly capture `this`, a pointer to current instance

             $\Rightarrow$ local member may be released while `lamda` func still using it

       6. in c++ 14: enable more type of var capture \& initialization of captured var

    4. functionality

       1. can NOT be assigned to other function (v.s. function pointer), by deleting assignment op
       2. can use copy constructor

    5. use case

       1. simple callback function: passed in as parameter, e.g. in `qsort`, ...
    
22. `std::function`

    1. erasure object: hide the detail of operations \& provide a uniform runtime interface
    2. coverability: any callable object
    
23. `static` 

    1. can be viewed as: a global variable associated/binded to a class

    2. $\Rightarrow$ need to not only declare in the class, but also declare again outside the class

       $\Rightarrow$ to register in the corresponding scope (outside a class instance)

       $\Rightarrow$ need to register only once, hence in `.cpp` 

       (potentially multiple declarations if declared in `.hpp`) 
       
    3. potential error
    
       1. multiple delarations, due to being declared in `hpp` \& included by multiple files
    
       2. undefined symbol, due to not declared outside the class and thus not recognized globally
    
          (especially when compiled into `.so`)

24. terminal arg with `gflags` 

    1. `DEFINE_xxx(arg, default value, help string)` to define a command line arg of type `xxx` 

    2. `FLAG_xxx` to access the passed in value for the previous arg with name `xxx` 

    3. `DECALRE_xxx` to re-use a `DEFINE`-ed args $\Rightarrow$ introduce dependency

       (hense, should only used in its test file or header file)

    4. `DEFINE_validator(arg, &func)` to check `arg` with `func`, to ensure valid passed-in value

    5. provide command line option `--flagfile=f` to read a file as passed-in args

    6. `ParseCommandLineFlags(&argc, &argv, true);` to parse the `argc, argv` passed to `main`

       1. the last argument set `remove_flags=true` to remove flag

          $\Rightarrow$ modify from`argv=["-q", "arg1"]` to `argv=["arg1"]` 

25. `std::move(x)` 

    1. `x`  is assumed to be expiring (a xvalue) $\Rightarrow$ take advantage of its allocated mem

       (while also ensuring `x` is correctly deconstructed)

    2. $\Rightarrow$ enable efficiently perform heavy object `return` by:

       1. disable normal copy construction `A(&A)=delete;` 
       2. enable move construction `A(&&A)=default;` 

       (move construction effectively enabled by compiler in c++17)

       (in C++11, the scope limits the use of move construction in `return`)

    3. enable efficient `vector` re-allocation

### Format Convention

- Instantiation
  - init list: as a col: each meber a line

### Template

- Compiler perspective

  - Dealing with Template

    - recognize template in `.hpp` in linking
    - compile all `.hpp` into library, generate template specialization for all encountered cases
    - compile `.cpp` & look up corresponding implementation for each usage of specialized template 

  - Pure Template all in `.hpp` 

    - recognized by `#include` & being specialized when used by other code with concret type

    - $\Rightarrow$ compiler auto-generates implementation before compiling `.cpp` 

      (yet may becomes gigantic; eventually very slow compilation)

  - Pure Template in `.hpp` & `.cpp`

    - pure template implmentation in `.cpp` NOT recognized in linking stage
    - canNOT auto-generate due to missing implementation 
    - $\Rightarrow$ report `undefined reference` error when looking up for `.cpp` 

  - Pure Template & Specialization in `.cpp`

    - no implementation recognized in linking stage
    - look up in pre-implementated cases, `undefined reference` if unforseen usage found
    - $\Rightarrow$ use with macro to make pre-spcialization (fast compilation)

#### CRTP - Curiously Recurring Template Pattern

- Definition

  ```c++
  template <typename T>
  struct Utils { // base
      void scale(double multiplicator);
      void square();
  private:
      // avoid... class T0: public Utils<T1>
      // => as constructors not accessible by T0, if not using T0 in template
      Utils(){}
      friend Utils<T>;
  };
  
  class Number : public Utils<Number> { // derived
  public:
      double getValue() const;
      void setValue(double value);
      // rest of the Number's rich interface...
  };
  
  template <typename T>
  struct Utils {
      // shorthand for static_cast
      T& underlying() { return static_cast<T&>(*this); }
      T const& underlying() const { return static_cast<T const&>(*this); }
      
      void scale(double multiplicator) {
          T& underlying = static_cast<T&>(*this);
          underlying.setValue(underlying.getValue() * multiplicator);
      }
      void square() {
          T& underlying = static_cast<T&>(*this);
          underlying.setValue(underlying.getValue() * underlying.getValue());
      }
  };
  ```

  - more implementation details: https://www.fluentcpp.com/2017/05/19/crtp-helper/

- Advantages

  - Adding Functionality as Interface

    - base class provides both interface definition \& implementation

      $\Rightarrow$ adding function while not implementing in derived class

      (compared with classic )

    - indeed, derived class becomes an entry to use the functionality in base class

  - Static Virtualization at Compile Time

    - base class binds with the each derived class at initializer call, at compile time

### Macro

- Capability

  - Class Generator

    - can be viewed as a self-defined proto $\Rightarrow$ define the `metaclass` 

  - Call-by-Name

    - pass in string \& call the func with that name

      $\Rightarrow$ generate if-else / switch table at compile time, according to defined classes

      (as class declaration written in macro - still class generator benefits)

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

    - hard to determine lock ownership: lock acquired by manipulating user-space memory

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

1. gc to release memory explicitly (gc.collect()) $\Rightarrow$ gc short for garbage collection

2. place the outer python-based library directly under the  folder => then import as usuall

3. In fact, **EVERYTHING** is object in python

4. python header files: /usr/include/python\*.\*m

5. `dict`

   1. `.pop(k, [d])`: pop out the item indexed by key `k`; 

      if key not found, return `d` if given, or an error raised 

6. symbol table: store all information related to a scope, actually, a `dict` in python
   1. `globals()` access the global symbol table
   2. `locals()` access the local symbol table

7. `**kwargs`: pass in arbitrary num of keyword argument

   1. indeed, organized into a `dict` 

### Numpy

1. matrix
    1. @：numpy 矩阵乘法 => can have difference between *,@,multiply,dot (for matrix/ndarray)
    2. explicit matrix inverse computations should be avoided for reasons of numerical stability
        => 用 numpy.linalg.solve()
    3. matrix[:,0:-1] => 矩阵第一列
        => 前闭后开
2. X = PolynomialFeatures(degree=2).fit_transform(X) => X每个features 的 2次多项式展开
3. np.random.shuffle(idx) => shuffle (index of) data set
4. 式子中改变+/- 号位置会影响精确度 => 调用opt.fmin_bfgs() 中有warning
5. U,S,V = np.linalg.svd(A)
    =>	S is 1-D array with corresponding number
      	V is matrix with row vectors being eigenvectors of A.T*A （U==V.T when A = X.T*X）
6. slicing & view
    1. `a[2:4,:]`: create a view on array, on the same memory of `a`
    2. `a[::-1]`: create a reversed view on array `a` 
    3. `a[3,...]`: `...` to set all axises not specified to be `:` 

- dType
  - type (e.g. `float`), size (in bytes), byte order
    - `dt = np.dtype('>i4')` $\Rightarrow$
    - `(dt.byteorder, dt.itemsize, dt.name, dt.type)` == `('>', 4, 'int32', np.int32)` 
- Structured Array (DataFrame)
  - specifying cols of arrays as `[(col_name, col_type), ...]` 
    
    - $\Rightarrow$ access cols using the `col name` - just as pandas
    
      e.g. `col = arr['col_name']`, where `col` an array specified by corresponding `col_type` (in trivial case, an $1$-D array) 
    
  - can have nested structure

### MatPlotLib

1. API Overview
1. `matplotlib.backend_bases.FigureCanvas` : the area onto which the figure is drawn, 
   2. `matplotlib.backend_bases.Renderer` : object knowing how to draw on `FigureCanvas``
   3. `matplotlib.artist.Artist` : object knowing how to use a renderer to paint onto the canvas

  $\Rightarrow$ `FigureCanvas` and `Renderer`  handle all the details of talking to UI toolkits

  $\Rightarrow$ `Artist` handles high level constructs e.g. representing and laying out the figure, text, and lines

### Function Decorator

special syntax sugar to manipulate the result of a function

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

### functools

- `partial(func, *args, **kwargs)`

  to generate function family (lots of similar functions) based on a root function

  - Params
    - `func` the python `function` object to be used
    - `*args, **kwargs` the args passed into the `func`
  - Understanding
    - “freezes” some arguments and/or keywords resulting in new func-obj with simplified signature

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

### Object & Memory

- `id`

  - show the identity (memory address) of an object $\Rightarrow$ used by `is` 

- Basic Data Types

  - `int, float, double, complex, string, bool`  

    - will not be associated \& can not be changed

  - created by variable copy

    - `v = 1`

      `l = [v] * 10`

    - `v, l[0], l[1]` will have same `id` on construction, as `l` contains copy of `v`, a pointer to `int` 

    - BUT, modifying either of them, would result in "copy on write"

      $\Rightarrow$ would not affect each other

- Complicated Types

  - `list, dict, set, ... & other container, & class` 

    - $\Rightarrow$ contain double pointer

  - created by cariable copy

    - `v = [1]`

      `l = [v] * 10`

    - will have the same `id`, as copying `v`, a pointer to a container `tuple` 

    - modify either of them would modify ALL of them

      $\Rightarrow$ NOT assignment, hence not recognized by python

      $\Rightarrow$ keep the same pointer (to a container), but the content inside is modifed 

      e.g. `v[0] = -1`, would result in `l == [[-1], [-1]]` 

    - yet, `l[0] = [-1]` can be recognized \& hence `l[1], v` not influenced

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
  - `threading.Lock()` 
    - return a `Lock` object, with `release()` and a potential blocking `acquire()` 
  - `thread.Thread(target=t, args=a)`
    - create a thread to run function `t` with arguments `a` passed in
  - 

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

### Py-Torch

- Type

  - `Torch.Size`
    - in fact a tuple

- Tensor

  - `.view()` 

    - as name, create another view on the same underlying data

      (change from one view influence other views)

  - `.numpy()` \& `.from_numpy()`

    - convert to/from a numpy ndarray \& sharing the same memory (if on cpu)
    - not supporting `char` array

  - `.to()`

    - memcpy tensor to the specified device
    - device denoted by a string (e.g. "cuda") or device-object (e.g. `torch.device("cuda")`)

  - `.require_grad`

    - attribute default to `False`, `True` to track all operation on the tensor
    - $\Rightarrow$ collect all gradients when `backward()` called \& accumulated to its `.grad` attribute

  - `.grad_fn`

    - hold \& register the `Function` that create the tensor (e.g. `+`, ...)

  - `.backward()` 

    - calc the grad, delete intermediary results (to reduce mem, as assumed to be no longer needed)

      $\Rightarrow$ cannot backprop twise (unless specifying `retain_graph=True`)

    - allow only scalar-to-tensor differentiation $\Rightarrow$ avoid intermediate high-dim tensor

    - $\Rightarrow$ for scalar `y`, `y.backward()` start the backprop from `y` 

    - $\Rightarrow$ for tensor `y`, `y.backward(w)` $\Leftrightarrow$ `l=torch.sum(y*w); l.backward()`, where `l` a scalar

      or equivalently, as `w` actually is the gradient for `y`, could take as passing down the chain rule

  - `.no_grad()`

    - not trigerring auto-diff system 

    - can be used as `with torch.no_grad()` 

      $\Rightarrow$ to be much efficient for doing model evaluation

- Modele

  - `.parameters()`
    - get all learnable params of a model
  - `.zero_grad()`
    - clear (set to 0) gradient buffers for all params in model
  - `.forward()`
    - can be user-defined \& assume $NCHW$ input (the first dim is always batch)
  - `.eval()`
    - notify all layers to be in eval mode (e.g. batchnorm, dropout, etc.)
  
- Transforms

  - utils for img processing, construct pipeline with `.compose([...])`:
    - data aug on img: crop, flip & rotation, resize, color, etc. (including user defined func)
    - on transforms itself: randomize, etc.

- Optimizer

  - Construction

    - popular optimizer implmented in `torch.optim`, 

      e.g. `optim.SGD(net.parameters(), lr=learning_rate, momentum=0.9)`

  - `.zero_grad()`

    - call `zero_grad()` of passed in parameters, e.g. `net.parameters()` 

  - Training

    - ```python
      # zero the parameter gradients
      optimizer.zero_grad()
      
      # forward + backward + optimize
      outputs = net(inputs)
      loss = criterion(outputs, labels)
      loss.backward()
      optimizer.step()
      ```

- `torch.distributed` \& `torch.multiprocessing` 

  - Creating Multi-Process
  
    - `torch.multiprocessing.spawn(func)` to start process group to run `func` 
  
    - `p = mp.Process(target=init_process, args=(rank, size, fn))` to create process
  
      \& `p.start()` to start runing, where `mp = torch.multiprocessing`
  
    - `new_group = dist.new_group(ids)` to create new group, where `ids` a list of process id (rank)
  
  - Process Initialization: Initial Coordination (`torch.distributed.init_process_group()`)
  
    - define environment variable: `WORKD_SIZE, RANK`
  
      where `world_size = dist.get_world_size()` = num of all processes; 
  
      ​			`rank = dist.get_rank()` = the id of each/current process 
  
      ​			(can be used to identify master-worker)
  
      (can read from `os.environ` if not set explicitly)
  
    - `local_rank`: device list to be used for the process, when using multi-gpu node
  
      (convension, rather than params of the call)
  
      $\Rightarrow$ use in each proc: `torch.cuda.set_device(local_rank)` 
  
    - init methods:
  
      1. file path on shared file system: use a shared file (with locking) for communication
  
         (all processes \& nodes need to have access to the file)
  
      2. master's IP-Port address: use TCP stack to excange info via rank-0 process (master)
  
      3. "env://": use TCP with master's IP-Port from `MASTER_ADDR, MASTER_PORT` in `os.environ` 
  
    - `torch.manual_seed()` to ensure all model construction start with same rand seed
  
      (if rand init involved)
  
    - **need such init for every process in the group** 
  
    - helper \& utilities
  
      1. launch utility `torch.distributed.launch` 
      2. starting distributed process `torch.multiprocessing.spawn` 
  
  - Sync Point for Processes
  
    - constructor, forward method \& differentiation of outputs (built-in)
    - `dist.barrier()` to manually sync all process in the group
  
  - Point-to-Point Communication
  
    - `send/recv (tensor, dst)`: blocking ops to transmit tensor between process (by rank num)
  
      receiver need to pre-allocate mem for incoming data $\Rightarrow$ need to have prior info of coming data
  
    - `isend/irecv` non-blocking conunterparts of `send/recv` 
  
      $\Rightarrow$ return a `Work` object that can be waited on (start trasmission earlier \& postpone the wait)
  
      (sender/receiver should not modify/access the tensor untile the `Work` finished) 
  
      i.e. not until `Work.wait()` finished
  
  - Collective Communication
  
    - `scatter` (multi-cast) - `gather`, `broadcast` - `all-gather` 
  
      e.g. `dist.scatter(tensor, src, scatter_list, group)`
  
    - `reduce` (tensor ops with multiple src and one output dst), `all-reduce` (output to every dst)
  
      e.g. `dist.all_reduce(tensor, op=dist.reduce_op.SUM, group=group)` 
  
  - General Training Procedure
  
    - init processes to have one model per process \& split the dataset accordingly
  
    - each model forward \& backward: compute local gradient
  
    - collect gradients from all other processes $\Rightarrow$ global average of gradient (for every param)
  
      (e.g. using `dist.all_reduce`)
  
    - each optimizer update the model params
  
  - Available Backend
  
    - Gloo: handy and easy
  
    - MPI: standardized \& highly optimized tool from high-performace computing
  
      (highly optimized on large computer cluster)
  
      $\Rightarrow$ MPI defines to maintain its own environment
  
      $\Rightarrow$ start from mpi instead: `mpirun -n 4 python myscript.py` 
  
    - NCCL: best optimized on CUDA
  
- DataParallel

  - `nn.DataParallel(model)`

    - replicate the model onto each gpu 

      e.g. `nn.DataParallel(net).to(device)`

    - split the data automatically (according to `batch_size` specified in `DataLoader`) 

      $\Rightarrow$ if $n$ gpu, then the model on each gpu gets $\frac {n}{\text{batch_size}}$ examples for one batch 

    - gather the result automatically:

      `output = net(input)` will have the `batch_size` examples as specified in `DataLoader` 

    - disallow direct access to the original model from the wrapped model

      (avoid collision as a set of new methods introduced by wrapping)

    - $\Rightarrow$ automatic multi-process

  - Self-Defined `DataParallel` Wrapper by `nn.parallel`

    -  using `nn.parallel` 

- ModelParallel

  - Device Control

    - fit sub-`nn.module` to different gpu by `.to(device)`$\Rightarrow$ in case of a too-large model

    - wrapping resnet to 2 gpu

      ```python
      from torchvision.models.resnet import ResNet, Bottleneck
      num_classes = 1000
      
      class ModelParallelResNet50(ResNet):
          def __init__(self, *args, **kwargs):
              super(ModelParallelResNet50, self).__init__(
                  Bottleneck, [3, 4, 6, 3], num_classes=num_classes, *args, **kwargs)
      
              self.seq1 = nn.Sequential(
                  self.conv1,
                  self.bn1,
                  self.relu,
                  self.maxpool,
      
                  self.layer1,
                  self.layer2
              ).to('cuda:0')
      
              self.seq2 = nn.Sequential(
                  self.layer3,
                  self.layer4,
                  self.avgpool,
              ).to('cuda:1')
      
              self.fc.to('cuda:1')
      
          def forward(self, x):
              x = self.seq2(self.seq1(x).to('cuda:1'))
              return self.fc(x.view(x.size(0), -1))
      ```

    $\Rightarrow$ recommend **in-class** device control, for better flexibility & maintainability

    ​	(can always assume input cpu data)

    - yet, performance significantly deteriorates:

      1. only 1 gpu is runing as a time (as sub-modules are not runing concurently)

      2. data transition between devices, yet a minor factor

         (as handled by nvLink and optimized by dedicated device)

  - Pipelining Inputs across Devices

    - in-model spliting the input for data pipeline construction (as using in-model device control)

    - leverage asynchronous launches of CUDA ops in py-torch

      $\Rightarrow$ no need for explicit threading (as cuda ops are already running concurrently)

    - `.to(device)` indicate device-to-device tensor copy $\Rightarrow$ create a synchronization point

      (sync on source \& destination devices)

      ```python
      class PipelineParallelResNet50(ModelParallelResNet50):
          def __init__(self, split_size=20, *args, **kwargs):
              super(PipelineParallelResNet50, self).__init__(*args, **kwargs)
              self.split_size = split_size
      
          def forward(self, x):
              splits = iter(x.split(self.split_size, dim=0))
              s_next = next(splits)
              s_prev = self.seq1(s_next).to('cuda:1') # prepare input for cuda:1
              ret = []
      
              for s_next in splits:
                  # A) s_prev runs on cuda:1
                  s_prev = self.seq2(s_prev)
                  ret.append(self.fc(s_prev.view(s_prev.size(0), -1)))
      
                  # B) s_next runs on cuda:0, which can run concurrently with A
                  s_prev = self.seq1(s_next).to('cuda:1') # sync by copy
      
              s_prev = self.seq2(s_prev)
              ret.append(self.fc(s_prev.view(s_prev.size(0), -1)))
      
              return torch.cat(ret)
      ```

    - could be further concurrent by overlapping copy of a tensor \& the compute of the other one

      $\Rightarrow$ using asynchronous copy

- Distributed Data Parallel (DDP): `DDP = torch.nn.parallel.DistributedDataParallel` 

  - v.s. Data Parallel

    - incorperates model parallel $\Rightarrow$ combine data parallel and model parallel

      (able to have data parallel on multiple models \& each model parallel on multiple devices)

    - provides parallelism for multi-process \& cross-machine enviornment

      (while data parallel is single-process \& multi-threaded )

  - Workload Balance

    - use `wait(timeout)` internally $\Rightarrow$ need to have each process has roughly same process speed
    - `timeout` set as initialization should account for inevitable skewed processing speed

  - Initialize Model `dp_model = DDP(model)`

    - `device_ids` = local gpu devices to use
    - `output_device` = device to gather model output

  - Save \& Load

    - save model from one process if enough 

      (as starts from same rand init \& gradients are synchronized)

    - `torch.load` with `map_location`: to map the loaded model into desired devices

      (otherwise, loaded into cpu first - all process contends for cpu)

  - Process \& Device Control

    - each process can have only one model: model relicas inside one process not supported

      $\Rightarrow$ one process per module replica (also, better performance)

    - model parallel with DDP: pass devices to model constructor

      (instead of using hard-coded device in `forward`)

  - Asynchronous Copy

    - host-to-device: use pinned (page-locked) memory \& `non_blocking=True` for `.to()`/`.cuda()` 

      (via `torch.Tensor.pin_memory()` or `Dataloader(pin_memory=True)`)

    - $\Rightarrow$ overlap GPU data transfer \& GPU computation

- Initialization Methods

- 

- Ring-Allreduce

  - 

- `hook`

  - `forward_hook`
  - `backward_hook`
  - 

## Distributed System

### Hadoop

- High Distributed File System (HDFS)
  - 
- Map Reduce (MR)
  - Divide-and-Conquer
  - Schdule
    - job client: produce MR job
    - job tracker
    - task tracker
  - Map
    - split the input data into `Mapper` 
    - result of `Mapper` is stored as file 
  - Reduce
    - shuffle: fetch data from each map result
    - merge the result
- Map Task
  - 

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



 ======================== Random Staff from Bachelor Life =================================== 

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

## TO DO List

### Paper Reading

- Stereo Vision
  
  - Geometry to the Rescue
  
  - Unsupervised Mono Depth Estimation with Left-Right Consistenct
  
    Unsupervised Learning of Depth and Ego-Motion from Video
  
    $\Rightarrow$ Digging Into Self-Supervised Mono Depth Estimation
  
  - Open-World Stereo Video Matching with Deep RNN
  
  - SIGNet: Segmantic Instance Aided Unsupervised 3D Geometry Perception
  
  -  Real-Time Dense Stereo Embedded in A UAV for Road Inspection

