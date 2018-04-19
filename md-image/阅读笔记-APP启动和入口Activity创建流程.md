##Android APP的启动和入口Activity的创建流程
##总览
>在Manifest.xml中定义Activity的时候，默认是属于所在包名的进程，在Activity启动的时候会首先检测当前Activity所属的进程是否已经启动，如果进程没有启动，则会先启动APP进程，在该进程启动之后才会执行入口Activity的启动过程。
>

###从进程创建和启动开始
- 进程创建的第一步:Zygote创建应用进程并实例化AcitivtyThread

>Zygote进程是所有android进程的父进程，包括 SystemServer和各种应用进程都是Zygote进程fork出来的。APP启动时ActivityManagerService通过socket通信的方式，调用Zygote来fork一个新的进程，通过反射创建出ActivityThread对象，并执行ActivityThread的main方法，该方法为用户进程的入口，也是APP的主线程的入口方法。

- ActivityThread.main()

>这个方法中主要执行一些初始化操作,
>>- 创建了一个UI线程消息队列，和Looper，所以我们可以在主线程随意创建Handler。

>>- 创建了一个ArrayMap用于保存四大组件中的Activity对象，Service对象，ContentProvider对象，而BroadcastReceiver则在使用时创建并运行，他的生命周期很短暂，没必要进行存储。
>>
>>- 创建了唯一的InitialApplication对象，即我们平常所使用的Application
>>
>>- 创建了的Instrumentation对象，用于对Activity进行操作，例如回调Activity的生命周期
>>
>>- 创建了ApplicationThread对象，是一个Binder实体对象，用于进程间通讯，主要和ActivityManagerService进行通信
>>
>>- 创建了ResourcesManager对象管理应用中的资源
>
随后调用ActivityThread.attach方法

- ActivityThread.attach()

>当main方法执行完毕后，则调用attach方法  
>>在attach方法中，ActivityThread主要通过ActivityManagerNative与ActivityManagerService通信，这是一个跨进程的通信，ActivityManagerNative作为Client，ActivityManagerService作为Server，在ActivityManagerNative中有Server的代理，这个代理从引用到实例的转化由binder驱动完成，随后ActivityManagerService的attachApplication方法被调用，随后Looper.loop()主线程进入loop方法轮询消息池

- ActivityManagerService.attachApplication()

>ActivityThread的attach方法通过binder调用了此方法 
>>在attach方法中，ActivityThread主要通过ActivityManagerNative与ActivityManagerService通信，传递ApplicationThread对象的引用，这是一个跨进程的通信，ActivityManagerNative作为Client，ActivityManagerService作为Server，在ActivityManagerNative中有Server的代理，这个代理从引用到实例的转化由binder驱动完成，拿到这个ApplicationThread对象的引用之后，ActivityManagerService就可以通过它来控制应用进程，随后通过拿到的ApplicationThread对象，即ActivityThread在ActivityManagerService进程中的代理，向ActivityThread的Handler发送msg，当Mainlooper轮询到这个msg后，执行ActivityThread的performLaucherActivity方法


###开始入口Activity的创建
- ActivityThread.performLaucherActivity()

>该方法执行入口Activity的创建，主要做了三件事

>- 通过反射创建Activity
>- 创建了一个ContextImpl对象
>- 让ContextImpl对象持有Activity的引用
>
>activity创建后，执行attack方法

- Activity.attack()

>该方法执行入口Activity的创建，主要做了两件事

>- 让Activity持有ContextImpl的引用，这样Activity与ContextImpl互相持有了对方的引用
>- 创建PhoneWindow和WindowManager并绑定

>attack方法执行后返回到performLaucherActivity方法继续向下执行Instrumentation的callActivityOnCreate方法

- Instrumentation. callActivityOnCreate()

>
>Activity的onCreate方法被调用,至此整个APP的进程的启动和入口Activity的创建结束

###流程图
![Alt text](https://raw.githubusercontent.com/cxt520ppgsh/md-image/master/md-image/activity%E5%90%AF%E5%8A%A8%E7%BC%A9%E7%95%A5%E5%9B%BE.png)