# RxJava

## Observer
观察者，它决定时间出发的时候酱油怎样的行为，`Observer`是一个接口：
```
Observer<String> observer = new Observer<String>() {
    @Override
    public void onNext(String s) {
        ...
    }

    @Override
    public void onCompleted() {
        ...
    }

    @Override
    public void onError(Thowable e) {
        ..
    }
}
```
RxJava还内置了一个实现了`Observer`的抽象类：`Subscriber`，`Subscriber`还增加了两个新方法：
+ **`onStart()`** 会在subsciber（订阅）刚开始，而事件还未发送之前被调用，默认实现为空， 该方法总是在subsciber所发生的线程被调用，而不能指定线程
+ **`unsubscriber()`** 用于取消订阅，该方法被调用后，`Subscriber`将不再接收事件，一般在调用前，可以用`isUnsubscribed`先判断一下状态

## Observable
被观察者， 它决定什么时候触发怎样的事件，它提供了众多静态方法用来创建一个`Observable`

### create()
```
Observable observable = Observable.create(new Observable.onSubscriber<String>) {
    @Override
    public void call(Subscriber<? super String> subscriber) {
        subscriber.onNext(...);
        subscriber.onCompleted();
    }
}
```

### just(T..)
将传入的参数依次发送出来
```
Observable observable = Observable.just(1, 2, 3)
```
订阅该`observable`的`Subscriber`会依次调用:
```
onNext(1);
onNext(2);
onNext(3);
onCompleted();
```

### from(T[]) / from(Iterable<? extends T>)
将传入的数组或`Iterable`拆分成具体的每一个对象，依次发送出来
```
String[] words = {"a", "b", "c"};
Observable observable = Observable.from(words)
```

## Subscribe 订阅
创建了`Observable`和`Observer`以后，调用`Observable.subscribe()`将二者关联起来
```
observable.subscribe(observer/subscriber);
```
`Observable.subscribe(Subscriber)`的实现的核心代码是：
```
public Subscription subscribe(Subscriber subscriber) {
    subscriber.onStart();
    onSubscribe.call(subscriber);
    return subscriber;
}
```


## Schedulers 线程控制
`Scheduler`调度器，RxJava通过它来指定每一段代码应该运行在什么的线程，下面是几个内置的`Scheduler`:

+ **`Schedulers.immediate()`**：直接在当前线程运行，相当于不指定线程，默认使用这个
+ **`Schedulers.newThread()`**：总是启动新线程，并在新线程执行操作
+ **`Schedulers.io()`**：I/O操作（读写文件，读写数据库，网络信息交互）所使用的`Scheduler`，行为模式和`newThread()`差不多，区别在于`io()`的内部实现是用一个无数量上限的线程池，可以重用空闲的线程，因此多数情况下比`newThread`更有效
+ **`Schedulers.computation()`**：计算所使用的`Scheduler`,这个计算指的是CPU密集型计算，即不会被I/O等操作限制性能的计算，如图性计算，这个`Scheduler`使用固定线程池，大小为CPU核数，不要把I/O操作放在`computation()`中，否则I/O操作的等待时间会浪费CPU
+ **`AndroidSchedulers.mainThread()`**：在Android主线程运行

使用`subscribeOn`和`observerOn()`两个方法并结合上述几个`Scheduler`来控制线程：

+ **`subscribeOn()`**： 指定`subscribe()`发生的线程，即`Observable.OnSubscribe`被激活时所处的线程
+ **`observerOn()`**：指定`Subscriber`所运行的线程
例子：
```
Observable testThread = Observable.create(new Observable.OnSubscribe<String>() {
            @Override
            public void call(Subscriber<? super String> subscriber) {
                Log.d(TAG, "\n");
                Log.d(TAG, "Observable thread is " + Thread.currentThread().getName());
                subscriber.onNext("Handsome");
                subscriber.onNext("Idiot");
                subscriber.onNext("Genius");
            }
        });
        testThread
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Action1<String>(){
                    @Override
                    public void call(String s) {
                        Log.d(TAG, "testThread: " + s);
                        Log.d(TAG, "Subscriber thread is " + Thread.currentThread().getName());
                    }
                });
```
> NOTE:`observeOn`可以调用多次，即进行多次`Subscriber`的线程切换， `subscribeOn`即使调用多次，也只有第一个`subscribeOn`起作用

### doSubscribeOn()
它和`Subscriber.onStart()`同样是在`subscribe()`调用后而且在事件发送前执行，但区别在于它可以指定线程，默认情况下，`doOnSubscribe()`执行在`subscribe()`发生的线程；而如果在`doOnSubscribe()`之后有`subscribeOn()`的话，它将执行在离它最近的`subscribeOn()`所指定的线程
```
Observable.create(onSubscribe)
    .subscribeOn(Schedulers.io())
    .doOnSubscribe(new Action0() {
        @Override
        public void call() {
            progressBar.setVisibility(View.VISIBLE); // 需要在主线程执行
        }
    })
    .subscribeOn(AndroidSchedulers.mainThread()) // 指定主线程
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(subscriber);
```
> NOTE:`doOnSubscribe`会运行在跟在他后面离他最近的`subscribeOn`所指定的线程，例子中就是Android主线程，但是`subscribe()`发生的线程(即`Observable.OnSubscribe`被激活时所处的线程)仍然只是由第一个`subscribeOn`指定，例子中即`Schedulers.io()`
