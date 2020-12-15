### 链表

链表作为一种最基础的数据结构，有着非常广泛的用途，因此其考察频率也相对较高。单一的链表作为知识点本身难度并不大，文中会列举出我在学习链表过程中遇到过的有意思的题目以及相对热门的面试考点。

***

1. 反转单链表

   反转单链表是十分高频的考题，做之前，简要介绍下链表常用的两种插入方法——头插与尾插。顾名思义，假设有一个链表的头节点head，尾节点tail，待插入节点p，头插法即将p指向head所指向的后继节点，再让head指向p；尾插法则是让尾节点指向p，再将p赋值给tail并修改next指针为空即可。

   要对链表进行反转，梳理下思路，假设有节点node，反转时只需后移一步，再指向前驱节点即可。移动后node需要指向前驱节点，也即移动前node的位置，因此需要额外引入指针pre来保存node的初始节点信息。node指向前驱节点的操作完成后，需知道后继节点才能向后移动，因此还需引入指针fol，在node移动前记录后继节点信息。

   ```c++
   //循环法实现
   void ReverseList(List *&head)
   {
       List *cur = nullptr ;//被操作节点
       List *fol = head ;//后继节点信息
       List *pre ;//前驱节点信息
       head->next = nullptr ;//头节点反转后为尾，需置为空指针
       while(fol != nullptr)
       {
           pre = cur ;
           cur = fol ;
           fol = fol->next ;
           cur->next = pre ;
       }
       head = cur ;
   }
   ```

   当然也可以用递归来实现，递归的思路就是从后向前推理，先找出终止条件（递归退出条件），显然本题的终止条件为遍历到空节点。

   ```c++
   //递归实现
   List ReverseList(List *head)
   {
       if(head->next == nullptr || head == nullptr)
           return head;
       List *temp = head->next ;//记录下个节点
       List *phead = ReverseList(temp) ;//先将下个节点带入递归
       temp->next = head ;//更改下个节点的指针
       head->next = nullptr ;//反转后头节点在尾部，需修改为空指针
       return phead ;
   }
   ```

   综上，反转单链表的两种方法就完成了。方法一使用静态函数，并直接在原链表上修改，操作完成后head依旧指向新的头节点。而方法二中，由于需要返还值来获取节点信息，故需定义为链表函数。

2. 如何检测单链表有环

   本题在C/C++、数据结构等相关知识提问环节出现的概率极高。首先要明白题意，单链表是只有一个后继指针的，记得曾和一位朋友交流时，他回答此题是可以直接遍历所有节点，因为存在环，故必然有节点存在两个后继指针导致分叉......这显然是理解层面上的反面教材。言归正传，检测单链表有环的方法网上应该可以查到不少，我能想到的最好理解的应该就是双指针法。<br/>
   设想下有一个环形操场，张三与李四站在起点处，哨声一响，两人同时出发，张三的速度为v，李四的速度是张三的两倍（或多倍），那么在运动距离上李四一定是领先张三的，而由于操场为环形，李四一定会再次与张三相遇，且相遇时领先的距离恰好等于操场的周长。那么反过来讲，速度不同，只要李四能与张三相遇（起点不算），就证明操场是一定环形的。这就是双指针的含义，指针ptr1以每次一个节点的速度移动，指针ptr2以每次两个（或多个）节点的速度移动，如果二者在后续能够相遇（即ptr1 == ptr2 || ptr1->next == ptr2），则可以证明该单链表存在环。

3. 链表与数组

   考题里常会问到链表与数组的优劣，其实属于常识类问题，稍微巩固一下数据结构的基础部分都不会有问题。

   数组的优点是可以随机存取，查改便利，当数组的规模非常大时会明显优于链表。数组的缺陷也很大，例如在进行插入或删除时会进行数据的大量拷贝，而链表只需通过节点指针的修改即可完成增删操作，再例如，数组存储数据是在连续的物理块上，而链表由于指针的缘故并没有此要求，所以数组会在一定程度上造成存储空间的浪费。

### 队列

队列是一种先进先出的数据结构，大致分为静态队列与链队列，分别用数组和链表来实现。用数组实现队列时通常为循环队列，可以减少空间资源的浪费。队列的考察多出现在解决其他数据结构的问题中，如二叉树的层次遍历等，这些将放在二叉树章节再具体详述。队列的常见考题都中规中矩，基础的部分需要稍加注意。

1. 循环队列的简单实现及判别条件

   ```c++
   //循环队列
   struct cir_queue{
       int front ;//头指针
       int rear ;//尾指针
       int arr[MaxSize] ;//数据类型可为任意
   };
   //循环队列判断是否为空
   bool is_empty(cir_queue que){
       if(que.front == que.rear)//头指针等于尾指针时为空
           return true ;
       else
           return false ;
   }
   //循环队列判断是否为满
   bool is_saturated（cir_queue que）{
       if((que.rear+1) % MaxSize == front)
           return true ;
       else
           return false ;
   }
   ```

   有一点需要注意，循环队列的空间利用率并不是100%，试想如果是完全利用率，队列空时头指针与尾指针是相等的，当我将数据存满MaxSize，队列的首尾指针再次相等了，这无法作为判别队列状态的条件，因此需要牺牲一个位置。即元素入队时，通常约定头指针先向前移动一步，再从移动后的位置来做增添操作；在出队列时，则需要尾指针的元素前移一步，无需额外操作。用这套规则，在判断队满时只需判别头指针再移动一个位置是否与尾指针相等即可。

2. 受限的双端队列简单实现

   ```c++
   //定长数组实现循环双端队列
   struct my_deque{
       int arr[MaxSize] ;
       int end1 ;//位置指针，end1只出不进
       int end2 ;//位置指针，end2可进可出
   };
   //判断队满
   bool is_saturated(my_deque deque){
       if((end2 + 1) % MaxSize == end1)
           return true ;
       else
           return false ;
   }
   //判断队空
   bool is_empty(my_deque deque){
       if(end1 == end2)
           return true ;
       else
           return false ;
   }
   //入队
   void push(my_deque deque, int data){
       if(!is_empty(deque)){
           end2 = (end2 + 1) % MaxSize ;
           arr[end2] = data ;
       }
   }
   //队首元素出队并将值返还给val
   void pop_back(my_deque deque, int &val){
       val = arr[end1] ;
       end1 = (end1 + 1) % MaxSize ;
   }
   //队尾出队，相当于弹栈
   void pop(my_deque deque, int val){
       val = arr[end2] ;
       end2 = (end2 - 1) % MaxSize ;
   }
   ```

   受限的双端队列集成了循环队列与栈的功能，位置指针end1与end2，与循环队列类似，空间利用率也无法达到100%。不同的是，受限的双端队列end1端受限只能出队，而end2端既可以入队也可以出队，当end2端出队时，此时队列功能相当于栈。以上代码写法较简短，也可以直接用类将所有函数封装在一起来使用。

3. C++ STL中队列常用API

   ```c++
   using std::queue ;
   queue<DataType> que ;//声明一个DataType类型的队列que
   que.front()//返回首部元素的引用
   que.back()//返回末位元素的引用
   que.push()//添加元素到末端
   que.size()//返回队列元素个数
   que.empty()//判断队列是否为空
   que.emplace()//用传入的参数调用队列的构造函数，在尾部生成对象
   que.swap(que2)//que与que2中的元素进行交换
   ```

   实际上，在STL中更加常用的是双端队列deque容器，deque为序列化容器，使用方法与vector类似，可在队列两端进行插入与删除操作，是C++基本容器之一。

   ```c++
   using std::deque ;
   deque<DataType> deque ;
   deque.begin()//指向首元素的迭代器
   deque.end()//指向末位元素的迭代器
   deque.size()//元素个数
   deque.max_size()//容器最大元素个数，不同于Capacity
   deque.front()//第一个元素的引用
   deque.back()//末位元素的引用
   deque.insert()//指定位置插入
   deque.erase()//移除指定位置元素
   //此外还有pop_back，pop_front，push_back，push_front等，用法类似vector，不再赘述
   ```

### 栈

与队列相似，后入先出的栈也是最常见的数据结构之一，并且在基础算法的考核中出现频率更高，当然栈的原理也很好理解，将其想象成一个只有入口的山洞，则最先进入洞内的人只能最后一位出洞，这就是栈的基本特点。同时，栈的很多典型应用也出现在二叉树的遍历算法中，这些将在二叉树部分再做详述，下文暂时列举出栈的常见题目作为参考。

1. 用两个栈来实现队列

   ```c++
   //使用两个栈就可以完全实现队列的功能
   struct my_stack{
       int arr[MaxSize] ;//任意数据类型皆可
       int top = -1 ;//栈顶指针
   };
   my_stack stk1, stk2 ;
   //模仿入队
   void push_que(int data){
       if(stk.top < MaxSize - 1)
           stk1.arr[++stk1.top] = data ;
   }
   //模仿出队,val接收出队元素
   void pop_que(int &val){
       if(stk2.top < MaxSize - 1){
           while(stk1.top >= 0){
               ++ stk2.top ;
               stk1.arr[stk1.top--] = stk2.arr[stk2.top] ;
           }
           val = stk2.arr[stk2.top--] ;
       }
   }
   ```

   用栈来模拟队列，只需注意入队数据只存入stk1，出队时要先把stk1中所有元素依次pop到stk2中，再进行栈顶元素读取以及修改stk2的top指针即可。

2. 符号匹配

   栈最常见的考题类型，做符号匹配的思路也较简单，当接收到"{","[","("时，字符入栈；当接收到"}","]",")"时，读取栈顶元素与接收到的字符进行匹配，若为"{}","[]","()"则匹配成功，栈顶元素出栈并开始下一个字符匹配，否则匹配失败；当接收的字符用完而栈不空时，匹配失败，示意代码如下：

   ```c++
   //此部分代码涉及实操，用stl实现
   using std::stack ;
   bool MyMatch(string str){
       stack<char> stk ;
       int i = 0 ;
       while(i < str.size()){
           if(str[i] == '{' || str[i] == '[' || str[i] == '(')
               stk.push(str[i]) ;
           else{
               char temp = str[i] ;
               switch(temp){
                   case '}':
                       if(stk.top() != '{')
                           return false ;
                       else{
                           stk.pop() ;
                           continue ;
                       }
                   case ']':
                       if(stk.top() != ']')
                           return false ;
                       else{
                           stk.pop() ;
                           continue ;
                       }
                   case ')':
                       if(stk.top() != ')')
                           return false ;
                       else{
                           stk.pop() ;
                           continue ;
                       }
               }
           }
       }
       if(!stk.empty())
           return false ;
       return true ;
   }
   ```

   
