最近在学习Python的多线程编程，写几篇文章记录一下。

# GIL
GIL是Global Interpreter Lock，即全局解释锁的缩写，保证了了同一时刻只有一个线程在一个CPU上执行字节码，无法将多个线程映射到多个CPU上。这是CPython解释器的缺陷，由于CPython是大部分环境下默认的Python执行环境，而很多库都是基于CPython编写的，因此很多人将GIL归结为Python的问题。

GIL被设计来保护线程安全，由于多线程共享变量，如果不能很好的进行线程同步，多线程非常容易将线程改乱。实际上即使有了GIL，这个问题也无法完全解决，因为GIL实际上也会释放，而且它并不是在某个线程执行完成后才释放，而是根据代码的字节码或者时间片进行释放，下面是一个例子：
```python
import threading

total = 0
def add():
    global total
    for i in range(1000000):
        total += 1

def desc():
    global total
    for i in range(1000000):
        total -= 1

thread1 = threading.Thread(target=add)
thread2 = threading.Thread(target=desc)
thread1.start()
thread2.start()
thread1.join()
thread2.join()

print(total)
```
这个程序直观来看，是将total加1000000减1000000，不管哪个线程先执行，最后的结果应该都是0才对，但是如果允许你该上面的代码多次，就会发现每次代码的结果都不一样，有正有负。这其中的原因就涉及到了GIL的释放。我们首先可以查看一下普通加法函数的字节码：
```python
import dis
def add1(a):
    a += 1
    return a
print(dis.dis(add1))
```
结果如下：
```
  2           0 LOAD_FAST                0 (a)
              2 LOAD_CONST               1 (1)
              4 INPLACE_ADD
              6 STORE_FAST               0 (a)

  3           8 LOAD_FAST                0 (a)
             10 RETURN_VALUE
None
```
可以看到a += 1的执行过程是先将变量a装载进CPU，再将常量1装载进CPU，然后执行相加操作，最后再将a存储在内存中。由于GIL不是根据Python代码段来释放，而是根据字节码或者时间片来释放的，在之前的例子中，如果add函数在进行加法后还未在内存中保存，GIL释放，desc函数获得执行权，此时它进行装载时装载的变量total是未进行加法操作的total，因此相当于之前的add函数失去了作用，在进行多次循环后，程序的运行结果自然不为0。这种情况称为竞态条件(race condition)，即使没有GIL，也会出现这种问题。解决方法是使用锁机制，将会在后面的文章中提到。

还有一种条件会导致GIL释放，那就是当程序遇到IO操作和time.sleep将程序阻塞的时候，因此多线程对于处理IO操作的问题非常有效。
***
# 多线程编程
多线程编程有两种方式，一种是通过Thread类对线程进行实例化，另外一种是通过继承Thread类并重写其run方法。

## 通过Thread类实例化进行多线程编程
下面是一个例子：
```python
import threading
import time

def do_something(content, sec):
    print('%s started' % content)
    time.sleep(sec)
    print('%s completed' % content)

def main():
    thread1 = threading.Thread(target=do_something, args=('First task', 2))
    thread2 = threading.Thread(target=do_something, args=('Second task', 4))
    start_time = time.time()
    thread1.start()
    thread2.start()
    print('Last time: %fs' % (time.time() - start_time))

main()
```
从这个例子中我们可以很容易地看出如果用Thread类实例化的方式创建线程，并通过start()方法开始线程。
结果输入如下：
```
First task started
Second task started
Last time: 0.000294s
First task completed
Second task completed
```
为什么这里的时间会是0s呢？原因是因为当我们创建了两个线程并启动后，此时的程序共有三个线程，thread1和thread2为子线程，main函数的线程被称为主线程，可在线程内部通过threading.current_thread()来获取当前线程信息，主线程会默认明名为'MainThread'，可在创建线程时使用参数name标记线程名。当开始了两个子线程后，由于三个线程并行，主线程也要继续运行，而执行两个start()方法的时间很短，所以打印的时间是0s。并且这里的输出结果包含了线程结束语句'... completed'，说明主线程运行结束，程序并没有退出，而是要等子线程运行结束后再退出。

如何使得主线程退出后子线程自动退出呢？只需要对子线程调用setDaemon方法，将子线程标记成守护线程，如下所示：
```python
def main():
    thread1 = threading.Thread(target=do_something, args=('First task', 2))
    thread2 = threading.Thread(target=do_something, args=('Second task', 4))
    thread1.setDaemon(True)
    thread2.setDaemon(True)
    start_time = time.time()
    thread1.start()
    thread2.start()
    print('Last time: %fs' % (time.time() - start_time))

main()
```
我们再运行程序，得到以下结果：
```
First task started
Second task started
Last time: 0.000235s
```
和我们预想的一致，主线程退出后，子线程也退出了，没来得及打印'... completed'语句。

另外一个问题是如何将主线程阻塞，等子线程运行完成后再继续主线程，这样我们就可以获得两个线程并行运行的时间了。只需要对子线程调用join方法就可以将主线程阻塞，如下所示：
```python
def main():
    thread1 = threading.Thread(target=do_something, args=('First task', 2))
    thread2 = threading.Thread(target=do_something, args=('Second task', 4))
    start_time = time.time()
    thread1.start()
    thread2.start()
    thread1.join()
    thread2.join()
    print('Last time: %fs' % (time.time() - start_time))

main()
```
运行结果如下：
```
First task started
Second task started
First task completed
Second task completed
Last time: 4.004622s
```
从结果可以看出，运行时间并不是串行的6s，而是两者中的大者，原因在前一章中提到了，当线程遇到IO操作或者time.sleep时，GIL会释放，将执行权留给其他线程。

## 通过继承Thread类并重写其run方法进行多线程编程
上面的方法适用于逻辑简单明确的情况，当代码逻辑复杂时，最好使用这种方法，方便代码维护。

如果你明白了上面的方法，只需要进行细微的改动即可，下面是一个例子：
```python
import threading

class DoSomething(threading.Thread):
    def __init__(self, content, sec):
        super().__init__()
        self.content = content
        self.sec = sec
    def run(self):
        print('%s started' % self.content)
        time.sleep(self.sec)
        print('%s completed' % self.content)

def main():
    thread1 = DoSomething('First task', 2)
    thread2 = DoSomething('Second task', 4)
    thread1.start()
    thread2.start()
    thread1.join()
    thread2.join()

main()
```
可以看出，threading.Thread类的start方法，实际上就是运行其run方法。
***
# 锁
在GIL中我们提到了竞态条件问题，即不同线程修改相同的共享变量出现运行多次结果不一样的问题，即使CPython中有GIL，这种问题依然存在。现在我们通过多线程的锁机制来解决这个问题。

还是相同的代码：
```python
import threading

total = 0
def add():
    global total
    for i in range(1000000):
        total += 1

def desc():
    global total
    for i in range(1000000):
        total -= 1

thread1 = threading.Thread(target=add)
thread2 = threading.Thread(target=desc)
thread1.start()
thread2.start()
thread1.join()
thread2.join()

print(total)
```
之前我们的分析的原因在于：两加法减法操作在底层实现的时候有多个步骤，由于GIL的切换导致字节码交替运行。如果我们能够保证实现加法或者减法操作的时候只有一个线程在运行，就能解决这个问题。而保证某一代码段只有一个线程在运行的方法就是为这个线程加锁，如下所示：
```python
import threading

total = 0
lock = threading.Lock()
def add():
    global total
    global lock
    for i in range(1000000):
        lock.acquire()
        total += 1
        lock.release()

def desc():
    global total
    global lock
    for i in range(1000000):
        lock.acquire()
        total -= 1
        lock.release()

thread1 = threading.Thread(target=add)
thread2 = threading.Thread(target=desc)
thread1.start()
thread2.start()
thread1.join()
thread2.join()

print(total)
```
运行结果为0。在上面的代码中，threading.Lock()实例化了一个锁对象，锁对象有两个方法：acquire和release，分别是获得锁和释放锁。当一个线程获得所时，另外一个线程在acquire处阻塞，直到当前锁执行release被释放后才可以和其他线程共同争夺锁。acquire和release之间的代码段执行时不会切换到其他线程，保证了操作的完整性。

用锁也存在问题，首先就是性能问题，在上面的例子中，不使用锁运行的执行时间是0.15秒，而使用锁执行时间是2.35秒，足足慢了15倍。

另外一个问题被称为死锁。当一个线程调用子程序时，如果这个子程序也需要加锁，则会出现这个问题：
```python
import threading
import time

lock = threading.Lock()

def do_something():
    global lock
    lock.acquire()
    do_sub_task()
    lock.release()

def do_sub_task():
    global lock
    lock.acquire()
    time.sleep(2)
    lock.release()

thread = threading.Thread(target=do_something)
thread.start()
thread.join()
```
程序会在do_sub_task的首句阻塞，因为该函数试图去获取锁，但是锁并没有释放。解决方法有两种：

- 一种是通过threading.Lock()再实例化一把锁，使得do_something和do_sub_task所需要的锁不是同一把，这样即使do_something获取了锁，do_sub_task也能够获得另外的锁。但是这种方式的问题是当这种情况出现很多，锁就很难管理。
- 另外一种是使用threading.RLock，这种锁可以重复获得，只要释放的次数等于获得的次数即可。将上面代码中的Lock换成RLock即可。

还有一种死锁情况称为互相等待，参看下面代码：
```python
import threading
import time

lock1 = threading.Lock()
lock2 = threading.Lock()

def do_something1():
    lock1.acquire()
    time.sleep(2)
    lock2.acquire()
    print('Something 1 started')
    time.sleep(2)
    lock1.release()
    lock2.release()
    print('Something 1 ended')

def do_something2():
    lock2.acquire()
    time.sleep(3)
    lock1.acquire()
    print('Something 2 started')
    time.sleep(3)
    lock2.release()
    lock1.release()
    print('Something 2 ended')

thread1 = threading.Thread(target=do_something1)
thread2 = threading.Thread(target=do_something2)
thread1.start()
thread2.start()
thread1.join()
thread2.join()
```
这个程序会一致阻塞，原因在于两个线程获得两个锁的顺序是相反的，当do_something1运行时获得lock1，然后执行time.sleep(2)使得GIL释放去执行do_something2。do_something2获得lock2后，同样执行time.sleep(3)使得GIL释放去执行do_something1，do_something1此时需要获得lock2才能继续执行，然而lock2在do_something2处，未释放无法获得。同理do_something2需要获得的lock1在do_something1处，也无法获得。所以就出现了两个线程互相等待的情况。如果将其中某个线程获得的锁的顺序交换，程序就能正常执行。

可以看出，使用锁机制很容易造成死锁，在使用锁的时候一定要小心。
***
# 生产者消费者问题
生产者消费者问题是多线程中一个很经典并发协作的问题，这个问题主要包含两类线程，一个是生产者用于生产数据，另一个是消费者用于消费数据，两者操作同一个数据共享区域，这种模型在编程中非常常见，比如爬虫，生产者负责爬取链接，消费者负责解析链接所指向的网页内容。这种模型需要满足下面的两个特征：

消费者在数据共享区域为空时阻塞，直到共享区域出现新数据。
生产者在数据共享区域满时阻塞，直到数据共享区出现空位。
下面是一个简单的例子：
```python
import threading
import time
import random
MAX_BUFF_LEN = 5

buff = []
lock = threading.Lock()

class Producer(threading.Thread):
    def run(self):
        global buff
        while True:
            lock.acquire()
            if len(buff) < MAX_BUFF_LEN:
                # 如果共享区域未满，生产数据
                num = random.uniform(0, 5)
                buff.append(num)
                print('生产者向共享区域加入%f' % num)
                lock.release()
            time.sleep(random.uniform(0, 10))

class Consumer(threading.Thread):
    def run(self):
        global buff
        while True:
            lock.acquire()
            if buff:
                # 如果共享区非空，消费数据
                num = buff.pop(0)
                print('消费者消费掉%f' %num)
                lock.release()
            time.sleep(random.uniform(0, 10))

producer = Producer()
consumer = Consumer()
producer.setDaemon(True)
consumer.setDaemon(True)
try:
    producer.start()
    consumer.start()
    producer.join()
    consumer.join()
except KeyboardInterrupt:
    print('程序强制结束！')
```
程序运行结果如下：
```
生产者向共享区域加入1.653411
消费者消费掉1.653411
生产者向共享区域加入2.176285
生产者向共享区域加入4.727504
生产者向共享区域加入3.053323
消费者消费掉2.176285
生产者向共享区域加入0.951072
消费者消费掉4.727504
^C程序强制结束！
```
在程序中设置两个进程为守护进程，并捕捉KeyboardInterrupt错误，一旦捕捉到就结束主线程，同时结束两个子线程。上面是一个生产者消费者模型的一个简单实现，通过共享变量的方式使两个线程互相通信来达成一致。共享变量是线程间通信的常用方法，只要记得在对共享变量进行操作时加锁，程序就不会有问题。

但是上面的代码也有问题，在于这种代码通过无限对共享变量访问的方式进行判断空还是满，这样也降低了效率。因为其中一个程序在明明知道buff满了或者空了的情况下还要进行无意义的循环，由于GIL机制，它会和其他线程争夺执行权。如果某一方在判断buff满了或者空了的情况下主动阻塞，直到另外一方通知它，它才恢复，这样就能最大化的效率。

Python中threading中的Condition类就是来帮助我们完成这件事的。它的wait和notify方法能够阻塞和通知一个线程，下面还是通过例子来了解一下：
```python
import threading
import time
import random
MAX_BUFF_LEN = 5

buff = []
condition = threading.Condition()

class Producer(threading.Thread):
    def run(self):
        global buff
        while True:
            condition.acquire()
            if len(buff) < MAX_BUFF_LEN:
                # 如果共享区域未满，生产数据
                num = random.uniform(0, 5)
                buff.append(num)
                print('生产者向共享区域加入%f' % num)
                condition.notify()
            else:
                # 如果共享区满，停止生产
                print('共享区满，生产者阻塞！')
                condition.wait()
            condition.release()
            time.sleep(random.uniform(0, 10))

class Consumer(threading.Thread):
    def run(self):
        global buff
        while True:
            condition.acquire()
            if buff:
                # 如果共享区非空，消费数据
                num = buff.pop(0)
                print('消费者消费掉%f' %num)
                condition.notify()
            else:
                # 如果共享去空，停止消费
                print('共享区空，消费者阻塞！')
                condition.wait()
            condition.release()
            time.sleep(random.uniform(0, 10))

producer = Producer()
consumer = Consumer()
producer.setDaemon(True)
consumer.setDaemon(True)
try:
    producer.start()
    consumer.start()
    producer.join()
    consumer.join()
except KeyboardInterrupt:
    print('程序强制结束！')
```
程序结果：
```
生产者向共享区域加入0.040350
消费者消费掉0.040350
共享区空，消费者阻塞！
生产者向共享区域加入3.266167
消费者消费掉3.266167
生产者向共享区域加入3.468917
^C程序强制结束！
```
上面的代码中，acquire方法实际上是获得锁，wait方法将线程阻塞，实际上是将锁释放。当一个线程调用notify方法时，另一个线程就被唤醒，但是这时候这个线程并没有调用wait或者release方法释放锁，因此另一个线程虽然醒过来了但是还是没有执行，直到这个线程将锁释放。

在使用共享变量的时候，需要时刻注意是否线程安全，非常不方便。好在是Python中提供了一个Queue类，它是线程安全的，有了它我们可以把注意力放在如何实现代码逻辑上，而不是过多的注意到线程安全上。在Python2.7中该模块名为Queue，而在Python3.6中该模块名为queue。使用Queue类改进的代码如下：
```python
import threading
import time
import random
from queue import Queue

MAX_BUFF_LEN = 5

buff = Queue(MAX_BUFF_LEN)
condition = threading.Condition()

class Producer(threading.Thread):
    def run(self):
        global buff
        while True:
            num = random.uniform(0, 5)
            buff.put(num)
            print('生产者向共享区域加入%f' % num)
            time.sleep(random.uniform(0, 10))

class Consumer(threading.Thread):
    def run(self):
        global buff
        while True:
            num = buff.get()
            print('消费者消费掉%f' %num)
            time.sleep(random.uniform(0, 10))

producer = Producer()
consumer = Consumer()
producer.setDaemon(True)
consumer.setDaemon(True)
try:
    producer.start()
    consumer.start()
    producer.join()
    consumer.join()
except KeyboardInterrupt:
    print('程序强制结束！')
```
Queue是一个FIFO队列，它的get方法和put方法分别是入队和出队，在入队和出队时获取了锁以保证线程安全，如果队列空或者满，默认情况下get方法和put方法自动阻塞。阻塞和唤醒的方式实质上是调用了Condition类的wait和notify方法。Queue类比较简单，推荐大家直接查看源码或者官方文档。

这里还有一篇写得非常好的博客，推荐大家去看看：[Producer-consumer problem in Python](https://www.agiliq.com/blog/2013/10/producer-consumer-problem-in-python/)
***
# 线程池
在之前的文章中我们一般只演示了两个线程的情况，在实际中我们要管理多个线程的时候就需要用到线程池。使用线程池管理线程能够使主线程可以获得某一线程的状态以及返回值，当一个线程完成的时候主线程就能立刻知道。

这里我们使用的线程池类是ThreadPoolExecutor，它在concurrent.futures下。concurrent.futures中还包括了ProcessPoolExecutor进程池对象，这个包的设计让多线程和多进程的接口一致。

下面是一个例子：
```python
from concurrent.futures import ThreadPoolExecutor
import time

def do_something(name, sec):
    print('Start doing %s' % name)
    time.sleep(sec)
    print('%s completed' % name)
    return name

executor = ThreadPoolExecutor(max_workers=2)
task = executor.submit(do_something, 'A', 2)
print(task.done())
print(task.result())
print(task.done())
运行结果：

Start doing A
False
A completed
A
True
```
首先需要实例化一个线程池对象，ThreadPoolExecutor类包含一个参数max_workers，表示最大同时运行的线程个数。线程池中可以加入任意多个线程，但是同时能运行的个数为max_workers，其他线程需要等当前正在运行的max_workers个线程运行完成才能运行。线程池对象的submit方法可传入一个函数句柄及它的参数，参数依次排列。一旦调用submit方法，线程就已经开始执行或即将执行，并返回一个Future对象。可调用Future对象的done方法查看线程是否执行完成，该方法非阻塞。还可以调用result方法获得线程的返回值，该方法阻塞直到线程结束得到返回值。

如果线程过多，可采用下面的写法：
```python
from concurrent.futures import ThreadPoolExecutor, as_completed
import random
...

all_task = [executor.submit(do_something, 'task_%d' %i, random.uniform(2,6)) for i in range(10)]
for future in as_completed(all_task):
    data = future.result()
    print(data)
```
这里的as_completed是一个生成器，它会生成已经完成的线程的future对象。先执行完成的线程的future对象会先被生成，直到所有线程结束，最后一个线程的future对象被生成。从结果来看，由于每次的线程切换不同，执行结果也不同。

另外还可以用ThreadPoolExecutor对象的map方法查询线程是否执行完成：
```python
for data in executor.map(do_something, ['task_%d' %i for i in range(10)], [random.uniform(2,6) for i in range(10)]):
    print(data)
```
和之前的as_completed方法不同，map生成器是按照参数的顺序返回的，但是线程执行依然是无序的。而且map返回的是线程的返回值，不是Future对象。在实践中最常用的还是第一种方法。

concurrent.futures还提供了wait方法，用于阻塞主线程。其用法是：
```python
from concurrent.futures import wait, ALL_COMPLETED, FIRST_COMPLETED, FIRST_EXCEPTION
wait(fs=all_task, return_when=ALL_COMPLETED)
```
第一参数fs是需要等待的线程列表，还有一个可选参数是return_when，即停止阻塞的条件，默认是ALL_COMPLETED，即所有线程完成。除此之外还包括：FIRST_COMPLETED（第一个线程执行完成后）、FIRST_EXCEPTION（在子线程中第一次出现抛出错误后）。


作者：NWKYEKJ  
链接：https://www.jianshu.com/p/beb094ae4261  
来源：简书  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。