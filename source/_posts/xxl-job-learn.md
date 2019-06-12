---
title: xxl-job-learn
date: 2019-06-12 11:37:03
tags: java
---

> 就是对 xxl-job 的学习记录

### [官方文档](http://www.xuxueli.com/xxl-job/#/?id=%E3%80%8A%E5%88%86%E5%B8%83%E5%BC%8F%E4%BB%BB%E5%8A%A1%E8%B0%83%E5%BA%A6%E5%B9%B3%E5%8F%B0xxl-job%E3%80%8B)

基本按照官方文档来就好了。下面是自己的坑。

- 执行器管理。第一次需要手动新增执行器（没认真看文档）

- job 可以传递参数，为 String 类型，默认为 ""。


### 源码学习

基本分为两部分： admin and executor. 就直接从一个任务执行开始看吧。

<!--more-->
#### JobHandler

新建一个 job 

```
/**
 * 任务Handler示例（Bean模式）
 * <p>
 * 开发步骤：
 * 1、继承"IJobHandler"：“com.xxl.job.core.handler.IJobHandler”；
 * 2、注册到Spring容器：添加“@Component”注解，被Spring容器扫描为Bean实例；
 * 3、注册到执行器工厂：添加“@JobHandler(value="自定义jobhandler名称")”注解，注解value值对应的是调度中心新建任务的JobHandler属性的值。
 * 4、执行日志：需要通过 "XxlJobLogger.log" 打印执行日志；
 *
 * @author xuxueli 2015-12-19 19:43:36
 */
@JobHandler(value = "demoJobHandler")
@Component
public class DemoJobHandler extends IJobHandler {

    @Override
    public ReturnT<String> execute(String param) {
        System.out.println("------ start ------- ");
        for (int i = 1; i < 100; i++) {
            System.out.println("loading......" + i);
        }
        System.out.println("------ end ------- ");
        return SUCCESS;
    }
}
```

很简单的注解

```
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
public @interface JobHandler {

    String value() default "";
    
}
```


然后在 XxlJobSpringExecutor 类里，进行初始化 （init JobHandler Repository）

```
public class XxlJobSpringExecutor extends XxlJobExecutor implements ApplicationContextAware {


    @Override
    public void start() throws Exception {

        // init JobHandler Repository
        initJobHandlerRepository(applicationContext);

        // refresh GlueFactory
        GlueFactory.refreshInstance(1);


        // super start
        super.start();
    }
}
```

主要在 ： initJobHandlerRepository 方法里进行初始化

```
private void initJobHandlerRepository(ApplicationContext applicationContext){
    if (applicationContext == null) {
        return;
    }
    // init job handler action
    Map<String, Object> serviceBeanMap = applicationContext.getBeansWithAnnotation(JobHandler.class);
    if (serviceBeanMap!=null && serviceBeanMap.size()>0) {
        for (Object serviceBean : serviceBeanMap.values()) {
            if (serviceBean instanceof IJobHandler){
                String name = serviceBean.getClass().getAnnotation(JobHandler.class).value();
                IJobHandler handler = (IJobHandler) serviceBean;
                if (loadJobHandler(name) != null) {
                    throw new RuntimeException("xxl-job jobhandler naming conflicts.");
                }
                registJobHandler(name, handler);
            }
        }
    }
```

#### IJobHandler


就是一个抽象类，定义了 一个 execute 方法

```
public abstract class IJobHandler {

	/** success */
	public static final ReturnT<String> SUCCESS = new ReturnT<String>(200, null);
	/** fail */
	public static final ReturnT<String> FAIL = new ReturnT<String>(500, null);
	/** fail timeout */
	public static final ReturnT<String> FAIL_TIMEOUT = new ReturnT<String>(502, null);

	/**
	 * execute handler, invoked when executor receives a scheduling request
	 *
	 * @param param
	 * @return
	 * @throws Exception
	 */
	public abstract ReturnT<String> execute(String param) throws Exception;

	public void init() {
	}

	public void destroy() {
	}
}
```

自定义job,通过 extends  IJobHandler，重写 execute 方法。

#### ApplicationContextAware

ApplicationContextAware 类的作用。一般我们直接在项目中，自动注入了。但是，如果 （an object requires access to a set of collaborating beans）。实现 ApplicationContextAware 更方便一些。

```
 * <p>Implementing this interface makes sense for example when an object
 * requires access to a set of collaborating beans. Note that configuration
 * via bean references is preferable to implementing this interface just
 * for bean lookup purposes.
```

例子：实现 ApplicationContextAware 接口

```
@Component
public class BeerApplicationContext implements ApplicationContextAware {

    private static ApplicationContext applicationContext;

    public static ApplicationContext getApplicationContext() {
        return applicationContext;
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        BeerApplicationContext.applicationContext = applicationContext;
    }
}
```

应用：

```
public void getBeanByName() {
    Object object = BeerApplicationContext.getApplicationContext().getBean("applicationContextService");
    System.out.println(object);
}
```

@Component 注解是必须的，加载到 spring bean 容器中。


但是，发现 XxlJobSpringExecutor 类，并没有 spring 的容器初始化注解。那肯定是在别的地方初始化了呗！果不其然，在 XxlJobConfig （执行器项目中）

```
 @Bean(initMethod = "start", destroyMethod = "destroy")
 public XxlJobSpringExecutor xxlJobExecutor() {
     logger.info(">>>>>>>>>>> xxl-job config init.");
     XxlJobSpringExecutor xxlJobSpringExecutor = new XxlJobSpringExecutor();
     xxlJobSpringExecutor.setAdminAddresses(adminAddresses);
     xxlJobSpringExecutor.setAppName(appName);
     xxlJobSpringExecutor.setIp(ip);
     xxlJobSpringExecutor.setPort(port);
     xxlJobSpringExecutor.setAccessToken(accessToken);
     xxlJobSpringExecutor.setLogPath(logPath);
     xxlJobSpringExecutor.setLogRetentionDays(logRetentionDays);
     return xxlJobSpringExecutor;
 }
```



#### XxlJobExecutor

XxlJobSpringExecutor extends XxlJobExecutor

```
public class XxlJobExecutor  {
    private static final Logger logger = LoggerFactory.getLogger(XxlJobExecutor.class);

    // ---------------------- param ----------------------
    private String adminAddresses;
    private String appName;
    private String ip;
    private int port;
    private String accessToken;
    private String logPath;
    private int logRetentionDays;

    // ---------------------- start + stop ----------------------
    start()
    stop()
}
```

一些属性的定义，以及 start and  destroy 方法

```
 @Bean(initMethod = "start", destroyMethod = "destroy")
 public XxlJobSpringExecutor xxlJobExecutor() {
    //.....
 }
```

##### start 方法

```
public void start() throws Exception {

        // init logpath
        XxlJobFileAppender.initLogPath(logPath);

        // init admin-client
        initAdminBizList(adminAddresses, accessToken);

        // init JobLogFileCleanThread
        JobLogFileCleanThread.getInstance().start(logRetentionDays);

        // init TriggerCallbackThread
        TriggerCallbackThread.getInstance().start();

        // init executor-server
        port = port>0?port: NetUtil.findAvailablePort(9999);
        ip = (ip!=null&&ip.trim().length()>0)?ip: IpUtil.getIp();
        initRpcProvider(ip, port, appName, accessToken);
}
```

方法往下点，最后点到了 [rpc项目](https://github.com/xuxueli/xxl-rpc/)


- init logpath

- init admin-client

- init JobLogFileCleanThread

- init TriggerCallbackThread

- init executor-server


##### destroy 方法

```
public void destroy(){
    // destory jobThreadRepository
    if (jobThreadRepository.size() > 0) {
        for (Map.Entry<Integer, JobThread> item: jobThreadRepository.entrySet()) {
            removeJobThread(item.getKey(), "web container destroy and kill the job.");
        }
        jobThreadRepository.clear();
    }
    // destory JobLogFileCleanThread
    JobLogFileCleanThread.getInstance().toStop();
    // destory TriggerCallbackThread
    TriggerCallbackThread.getInstance().toStop();
    // destory executor-server
    stopRpcProvider();
}
```

1.  destory jobThreadRepository

```
if (jobThreadRepository.size() > 0) {
    for (Map.Entry<Integer, JobThread> item: jobThreadRepository.entrySet()) {
        removeJobThread(item.getKey(), "web container destroy and kill the job.");
    }
    jobThreadRepository.clear();
}

public static void removeJobThread(int jobId, String removeOldReason){
    JobThread oldJobThread = jobThreadRepository.remove(jobId);
    if (oldJobThread != null) {
        oldJobThread.toStop(removeOldReason);
        oldJobThread.interrupt();
    }
}
```

ConcurrentHashMap<Integer, JobThread> jobThreadRepository

key 为 jobId, value 为 JobThread。

```
if (oldJobThread != null) {
        oldJobThread.toStop(removeOldReason);
        oldJobThread.interrupt();
}

//没看懂什么意思。后续更新 todo
public void toStop(String stopReason) {
	/**
	 * Thread.interrupt只支持终止线程的阻塞状态(wait、join、sleep)，
	 * 在阻塞出抛出InterruptedException异常,但是并不会终止运行的线程本身；
	 * 所以需要注意，此处彻底销毁本线程，需要通过共享变量方式；
	 */
	this.toStop = true;
	this.stopReason = stopReason;
}
```

2. destory JobLogFileCleanThread
3. destory TriggerCallbackThread
4. destory executor-server

#### XxlJobSpringExecutor

```
@Override
public void start() throws Exception {
    // init JobHandler Repository
    initJobHandlerRepository(applicationContext);
    // refresh GlueFactory
    GlueFactory.refreshInstance(1);
    // super start
    super.start();
}
```

initJobHandlerRepository 

```
 private void initJobHandlerRepository(ApplicationContext applicationContext){
     if (applicationContext == null) {
         return;
     }
     // init job handler action
     Map<String, Object> serviceBeanMap = applicationContext.getBeansWithAnnotation(JobHandler.class);
     if (serviceBeanMap!=null && serviceBeanMap.size()>0) {
         for (Object serviceBean : serviceBeanMap.values()) {
             if (serviceBean instanceof IJobHandler){
                 String name = serviceBean.getClass().getAnnotation(JobHandler.class).value();
                 IJobHandler handler = (IJobHandler) serviceBean;
                 //防止重名的 job 
                 if (loadJobHandler(name) != null) {
                     throw new RuntimeException("xxl-job jobhandler naming conflicts.");
                 }
                 //注册 ConcurrentHashMap<String, IJobHandler> jobHandlerRepository
                 registJobHandler(name, handler);
             }
         }
     }
 }
```

Map<String, Object> serviceBeanMap = applicationContext.getBeansWithAnnotation(JobHandler.class);


getBeansWithAnnotation ,获取 map (Find all beans which are annotated with the supplied {@link Annotation} type,)

```
/**
 * 通过  Service 注解获取 bean
 */
public void getBeanByNameMap() {
    Map<String, Object> serviceMap = BeerApplicationContext.getApplicationContext().getBeansWithAnnotation(Service.class);
    for (String key : serviceMap.keySet()) {
        System.out.println(key + " " + serviceMap.get(key));
    }
}

//结果
applicationContextService com.beer.xxljoblearn.service.ApplicationContextService@12aa1a3
commonService com.beer.xxljoblearn.service.CommonService@765ed5
```
#### JobThread

上面都是在应用启动的时候， 初始化 bean, 加载 bean. 那么每个任务是怎么执行起来的呢？


run 方法，实际就是 new 了一个 thread, 然后执行 IJobHandler 的 execute 方法
```
public void run(){
    ...

    //核心方法
    Thread futureThread = null;
    try {
        final TriggerParam triggerParamTmp = triggerParam;
        FutureTask<ReturnT<String>> futureTask = new FutureTask<ReturnT<String>>(new Callable<ReturnT<String>>() {
            @Override
            public ReturnT<String> call() throws Exception {
                return handler.execute(triggerParamTmp.getExecutorParams());
            }
        });
        futureThread = new Thread(futureTask);
        futureThread.start();
        executeResult = futureTask.get(triggerParam.getExecutorTimeout(), TimeUnit.SECONDS);    
    }
    
    ...
}
```

pushTriggerQueue . 添加一个触发器到队列。

```
/**
 * new trigger to queue
 *
 * @param triggerParam
 * @return
 */
public ReturnT<String> pushTriggerQueue(TriggerParam triggerParam) {
	// avoid repeat
	if (triggerLogIdSet.contains(triggerParam.getLogId())) {
		logger.info(">>>>>>>>>>> repeate trigger job, logId:{}", triggerParam.getLogId());
		return new ReturnT<String>(ReturnT.FAIL_CODE, "repeate trigger job, logId:" + triggerParam.getLogId());
	}
	triggerLogIdSet.add(triggerParam.getLogId());
	triggerQueue.add(triggerParam);
    return ReturnT.SUCCESS;
}
```

此处没有用 Thread.interrupt 方法，解释的很清楚了。通过共享变量 toStop 来判断线程的状态

```
/**
 * kill job thread
 *
 * @param stopReason
 */
public void toStop(String stopReason) {
	/**
	 * Thread.interrupt只支持终止线程的阻塞状态(wait、join、sleep)，
	 * 在阻塞出抛出InterruptedException异常,但是并不会终止运行的线程本身；
	 * 所以需要注意，此处彻底销毁本线程，需要通过共享变量方式；
	 */
	this.toStop = true;
	this.stopReason = stopReason;
}
```

参考： https://www.cnblogs.com/jenkov/p/juc_interrupt.html

```
if (oldJobThread != null) {
    oldJobThread.toStop(removeOldReason);
    oldJobThread.interrupt();
}
```

isRunningOrHasQueue

```
/**
 * is running job
 * @return
 */
public boolean isRunningOrHasQueue() {
    return running || triggerQueue.size()>0;
}
```


#### 看不懂了

后面都是 rpc 的包的内容
