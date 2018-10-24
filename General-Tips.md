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

### Ada

1. Only operator "=" can be promoted.
2. for all i in range => suggest concurrent performance.
3. modular type can NOT be runtime dependent.
4. can NOT have variant record in array. => compiler refuse to alloct heap mem at runtime
    => but CAN use mutable variant record (where a default value is given)
    ​	=> but can vary in the fly.
5. Make sure sub-task get the termination signal before main task terminated by either
  - terminating safely, or 
  - raising an error

### Python

1. @：矩阵乘法 => can have difference between *,@,multiply,dot (for matrix/ndarray)
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
10. gc to release memory explicitly (gc.collect())
11. place the outer python-based library directly under the  folder => then import as usuall
12. python multi-inheritance: 
    ​    - Method Resolution Order (MRO)
    ​      - maintain the precedence appeared in the inheritance list in the derived class
    ​      - super() call:
    ​        1. find & gather the next class in MRO list into a stack
    ​        2. init & pop the class from that stack $\Rightarrow$ last in first out
      - Passing Parameters
        - bass class need to be designed for multi-inheritance
        - $\Rightarrow$ intermediate class need to take extra parameters to pass along the MRO
        - $\Rightarrow$ force designer to be aware of the MRO precedence!

### Octave / Matlab

1. unrolling parameters: deltaVector = [ D1(:) ; D2(:) ] // matrix D1,D2 $\Rightarrow$ column vector 
2. Reshape: D1 = reshape (deltaVector(1, m\*n), m, n) // column vector $\Rightarrow$ m\*n matrix

### Data

1. h5py is a good thing: light-weight dataset structure
2. TF.Data: a pipeline for fetching, parsing and feeding the data (in a separate thread $\Rightarrow$ parallel)

### QT

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