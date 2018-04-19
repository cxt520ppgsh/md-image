###glide笔记()
####主要方法解析
#####glide.create()   
作用: 通过GlideBuilder创建实例，有四个参数
>①MemeryCache 
>>内存缓存，大小默认为屏幕宽*高*4*2

>>具体实现为LruResourceCache 

>>把最近使用的对象用强引用存储在LinkedHashMap中，并且把最近最少使用的对象变为弱引用存储


>②BitmapPool
>>图片池，一般是缓存大小是屏幕的宽 * 高 * 4 * 2

>>具体实现为LruBitmapPool

>>保留最近使用Bitmap的大小

>>用于重用bitmap对象，避免无用bitmap的频繁gc导致内存抖动

>③DecodeFormat
>>编码格式，默认是RGB_565
>
>④Engine
>引擎类，主要执行图片资源的处理，内存缓存的处理，磁盘缓存的处理
有四个主要工作类
>>①memorycache
>>>如上述所提

>>②diskCacheFactory
>>>本地缓存,默认大小:250 MB

>>③sourceService
>>>处理资源线程池
核心线程数等于处理器个数

>>④diskCacheService
>>>处理本地缓存线程池
核心线程数量为1

#####Glide.with(context)  
作用: 绑定组件生命周期
>①根据context类型的不同返回不同的RequestManager
>
>②假如context instanceof Activity，创建一个叫做RequestManagerFragment的无界面fragment，通过FragmentManager绑定该Activity，绑定RequestManager
>
>③当Activity生命周期回调时由于Fragment特性，RequestManagerFragment也进行相应的回调，同时回调到绑定的RequestManager进行glide的生命周期操作


#####Glide.load(String string)
>设置需要加载的资源路径


#####Glide.into(Imageview imageview)
>①判断是否是否是主线程，如果不是抛出异常
>
>②根据Imageview的ScaleType和Transform方法对图片进行处理
>
>③将Imageview封装成Target，用于存储请求，当控件复用时，如果旧的请求还在，则绑定新的请求，以此解决图片错位
>
>④创建请求，调用Engine的Load方法


#####Engine.load()
>①从MemeryCache查找是否有该请求的资源的缓存
>>如果有则取出，删除MemeryCache中对应的cache，然后加入名叫activeResources的以弱引用为值的map，为了避免内存不足清除MemeryCache中正在使用中的资源
>>如果没有则从activeResources中取，如果activeResources中依旧没有该请求的资源的缓存，则通过EngineJobFactory创建一个EngineJob，在EngineJob中处理请求
>>

>②创建EngineJob
作用: 进行图片的加载,管理diskCacheService和sourceService

>>diskCacheService:首先请求封装为Runnable分发到diskCacheService的task中，让线程池去执行任务，处理任务的逻辑是，首先尝试从磁盘缓存中读取，如果有的话通过onLoadComplete方法进行文件到内存的转化,加入到内存缓存，显示到该ImageView上，如果没有的话，则将Runnable分发到sourceService中

>>sourceService:接收diskCacheService分发的Runnable任务，首先通过Http请求获取网络图片资源的数据流，通过decodeFromSourceData对图片数据流进行一系列的解码和裁剪等处理，将处理后的图片资源写入磁盘缓存和内存缓存
>>

####加载流程图
>![Alt text](http://code.lukou.com/chenxutang/md-image/raw/master/md-image/glide%E5%9B%BE%E7%89%87%E5%8A%A0%E8%BD%BD%E6%B5%81%E7%A8%8B.png)
