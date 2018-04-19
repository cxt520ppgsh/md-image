##RxJava解析
###从一次Helloword开始讲起
>
```java
	Observable.just("Hello world")
	        .subscribe(word -> {
            System.out.println("got " + word + " @ " 
	                    + Thread.currentThread().getName());
        });
```

###just():

>创建Observable 创建了一个OnSubscribe的回调，通知Observable自己被订阅

###subscribe()：
>
>首先对传入的Action进行包装，包装成一个Subscriber的实现类
调用subscriber.onStart()回调onSubscribe通知Observable被订阅，通知Observable的数据发送,默认在调用subscribe()的线程执行，同时建立Observable和Subscriber的联系,调用了observable.onSubscribe.call(subscriber)方法，开启数据流

>在Rx1.x版本，数据驱动交给Observable执行，但是一旦生产者生产速度超过消费者处理速度，就会出现数据的停滞，RX的解决办法是把阻塞的数据保存起来，或者丢掉多余的数据，保存和丢失按需而定。另一种方法是让 subscriber 向 observable 主动请求数据，避免出现过多的数据

###request():

>Observable调用subscriber 的 onNext() 和 onCompleted()



###map():

>①利用传入的 subscriber 以及我们进行转换的 Func1 构造一个 MapSubscriber

>②把一个 subscriber 加入到另一个 subscriber 中，让它们可以一起取消订阅。
在MapSubscriber中进行转换，在传递到下游的Action1


###线程调度

>RxJava中 使用observeOn和subscribeOn操作符，可以让Observable在一个特定的调度器上执行，observeOn指示一个Observable在一个特定的调度器上调用观察者的onNext, onError和onCompleted方法，subscribeOn则指示Observable将全部的处理过程（包括发射数据和通知）放在特定的调度器上执行。
>

###subscribeOn()原理

>指定Observable中OnSubscribe(计划表)的call方法在那个线程发射数据
创建了新的Observable，为新的Observable创建计划表对象OperatorSubscribeOn，这个对象保存着原始Observable对象和调度器scheduler

>在call方法中通过scheduler.createWorker().schedule()完成线程的切换
这里的scheduler就是subscribeOn(Schedulers.newThread() )传入的对象，
在新产生的OperatorSubscribeOn计划表中通过NewThreadWorker.schedule(Action0)将任务放入线程池中执行
Scheduler与Worker

###Scheduler
>Scheduler是任务的线程调度器，具有很多的实现类，如IoScheduler是专门处理IO线程的调度器，相当于线程的指挥者

>Worker：线程任务的具体执行者，也具有很多的实现类，在不同


