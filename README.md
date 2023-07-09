# My-notes
我的找工作面试记录。

关于STL标准模板库的笔记知识点：
STL共有六大部件：Containers（容器）、Iterators（迭代器）、Algorithms（算法）、Allocators（分配器）、Adapters（适配器）、Functors（仿函数）。

快手客户端开发面试经历：7.3下午投递简历，7.4中午约面试，7.5下午三点一面，五点半通知一面通过，7.6下午三点二面。

快手一面面经：
1.进程与线程的特点与区别：
引入线程，用来提交程序并发执行的程度，减少程序在并发执行时所付出的时空开销，使OS具有更好的并发性。
①拥有资源：进程可以拥有资源，（进程是拥有资源的基本单位）是资源分配的最小单位；而线程本身并不拥有系统资源，只拥有能保证独立运行的资源，如TCB等，线程除了拥有自己的少量资源外，还允许多个线程共享该进程所拥有的资源，表现在：属于同一个进程的所有线程都具有相同的地址空间，线程可以访问该地址空间中的每一个虚地址，还可以访问进程所拥有的资源，如已打开的文件、定时器、信号量等；
②调度的基本单位：线程是系统调度的基本单位，因此线程是能独立运行的基本单位，线程切换时，切换代价远低于进程。在同一进程中，线程的切换不会引起进程的切换，但从一个进程中的线程切换到另一个进程中的线程，必然就会引起进程的切换；
③并发性：不仅进程之间可以并发执行，而且在一个进程之间的多个线程之间亦可并发执行，甚至还允许在一个进程中的所有线程都能并发执行；
④独立性：在用一个进程中的不同线程之间的独立性要比不同进程之间的独立性低得多。同一进程的不同线程往往是为了提高并发性以及进行相互之间的合作而创建的，它们共享进程的内存地址空间和资源，如每个线程都可以访问它们所属进程地址空间中的所有地址。
2.进程间通信方式和线程间通信方式：
同步方式：信号量机制、管道、消息队列；信号量：是一个计数器，可以用来控制多个进程对临界资源的访问。信号量用于实现进程间的互斥与同步。P操作（递减操作）可以用于阻塞一个进程，V操作（增加操作）可以用于解除阻塞一个进程。
进程间通信方式：
3.哈希表底层原理

4.TCP和UDP的区别

5.TCP四次挥手

6.HTTP1.0和HTTP2.0的区别，HTTP2.0的缺点是什么，如何改进？

手撕代码题：
1.问到线程间通信时说了信号量，用pv操作来实现同步和互斥的关系：盘子里有最多可以装两个水果，苹果和橘子，爸爸每次往盘子里放一个苹果，妈妈每次往盘子里放一个橘子，儿子每次从盘子里拿一个苹果，女儿每次从盘子里拿一个橘子，请用信号量实现同步和互斥：
```C++
#include <pthread.h>
#include <semaphore>
// mutex1是苹果互斥锁，mutex2是橘子互斥锁
mutex_t mutex1, mutex2;
// sem1是苹果信号量，sem2是橘子信号量
sem_t sem1, sem2;
pthread_mutex_init(mutex1, NULL);
pthread_mutex_init(mutex2);
sem_init(sem1);
sem_init(sem2);
void father() {
    pthread_mutex_lock(mutex1, NULL);
    sem_post(sem1);
    pthread_mutex_unlock(mutex1, NULL);
void son() {
    pthread_mutex_lock(mutex2, NULL);
    sem_wait(sem1);
    pthread_mutex_unlock(mutex2, NULL);
}

pthread_mutex_destroy(mutex1);
pthread_mutex_destroy(mutex2);
sem_destroy(sem1);
sem_destroy(sem2);
```

2.二叉树前序遍历非递归（用栈呗）

3.leetcode1658题，将x减到0的最小操作数（这个想了半天不会，提示了用滑动窗口还是不会，于是跳过换一题）

4.就地反转链表



快手二面面经：
面试官看了一面问的是操作系统和计算机网络的题，于是开始问C++的题，问的比较仔细。
1.值传递、引用和指针的区别，主要是引用和指针的区别，各自的优点和适用环境是什么：

2.malloc的使用，参数和返回（说没怎么用过，面试官说没关系，然后一点一点说怎么去设计，给什么参数，返回什么）：

3.接上一个题，面试官说malloc返回的类型是void*，然后说int型和double型是不可以互相转换的，因为会损失精度，那么int*和double*都可以和void*类型转换，就相当于int*和double*相互转换，这样是不是不太合理的样子：

4.手撕LRU（很幸运上午十一点多在教研室刚手撕了这个题，惊喜，不然肯定不会做），撕了挺长时间的
```C++
struct DListNode {
    int key, value;
    DListNode* pre;
    DListNode* next;
    DListNode():key(0), value(0), pre(nullptr), next(nullptr) {}
    DListNode(int _key, int _value):key(_key), value(_value), pre(nullptr), next(nullptr) {}
};

class LRUCache {
public:
    LRUCache(int capacity) {
        this->capacity = capacity;
        this->size = 0;
        head = new DListNode();
        tail = new DListNode();
        head->next = tail;
        tail->pre = head;
    }
    ~LRUCache() {
        delete head;
        delete tail;
    }
    int get(int key) {
        if (cache.count(key) == 0) {
            return -1;
        }
        DListNode* tmp = cache[key];
        remove(tmp);
        addToHead(tmp);
        return tmp->value;
    }
    void put(int key, int value) {
        if (cache.count(key) == 0) {
            if (size >= capacity) {
                DListNode* real_tail = removeTail(); // 直接删就行
                DListNode* real_head = new DListNode(key, value);
                addToHead(real_head);
            } else {
                DListNode* real_head = new DlistNode(key, value);
                addToHead(real_head);
            }
        } else {
            DListNode* tmp = cache[key];
            tmp->value = value;
            addToHead(tmp);
        }
    }

    void addToHead(DListNode* node) {
        node->pre = head;
        node->next = head->next;
        head->next->pre = node;
        head->next = node;
        size++;
    }
    void remove(DListNode* node) {
        node->pre->next = node->next;
        node->next->pre = node->pre;
        size--;
    }
    DListNode* removeTail() {
        DListNode* tmp = tail->pre;
        tmp->next->pre = tmp->pre;
        tmp->pre->next = tmp->next;
        return tmp;
    }

private:
    // hash表可以实现O(1)的查找复杂度，链表可以实现O(1)的增删复杂度，太完美了啊！
    unordered_map<int, DListNode*> cache;
    DListNode* head; // 虚拟头节点
    DListNode* tail; // 虚拟尾节点
    int size;
    int capacity;
};

```


5.编译的过程

6.情景题，
