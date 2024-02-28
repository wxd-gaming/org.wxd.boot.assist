# org.wxd.boot.assist



## 介绍
通过assist字节码增强做到对项目调用路由链的耗时监控

## 软件架构
软件架构说明

通过启动参数增加
-javaagent:libs\wxd-j21-assist.jar=需要监控的包名<br>
jar包的路径位置和需要你监控的包名。多个包名可以用 “|” 进行切割

示例代码在 src/test/java/code/RuntimeMonitorTest

需要监控的类可以通过实现 IAssistMonitor 接口来增强监控的实现，可以重新里面方法<br>
IAssistOutFile 接口的实现可以达到对增强后字节码文件输出，方便你查看增强后字节码文件内容

输出目录target/assist-out/

```java
    @Test
    @MonitorAlligator //注解是用于屏蔽这个方法不需要增强监控，
    public void at1() throws Exception {
        MonitorRecord.MonitorStack monitorStack = AssistMonitor.start();//自己实现了监控的开始
        /**如果要使用耗时统计添加启动参数 -javaagent:..\target\libs\assist.jar=需要监控的包名 */
        at2();
        at3();
        AssistMonitor.close(monitorStack, this);//增强结束
    }
```
```java
    public static class C implements IAssistOutFile {
        public void c1() throws InterruptedException {
            new T2().t2();
        }
    }
    
    // 字节码增强后输出

    public static class C implements IAssistOutFile {
        public C() {
        }

        public void c1() throws InterruptedException {
            MonitorRecord.MonitorStack monitorStack = AssistMonitor.start();
            (new T2()).t2();
            Object var3 = null;
            AssistMonitor.close(monitorStack);
        }
    }
```

```
----------------------------------start--------------------------------------
[2024/01/09 10:08:15:327] 初始化完成 args null
-----------------------------------end-------------------------------------

----------------------------------start--------------------------------------
[2024/01/09 10:08:15:599] [Thread[#1,main,5,main]] - 文件：RuntimeMonitorTest.java, 方法：code.RuntimeMonitorTest.at2
堆栈：
|code.RuntimeMonitorTest.at2:32
|_code.RuntimeMonitorTest$B.b1:63 耗时：18.65 ms
|__code.RuntimeMonitorTest$A.a1:50 耗时：18.58 ms
|___code.RuntimeMonitorTest$A.a2:55 耗时：18.5 ms
|____code.T2.t2:10 耗时：4.2 ms
|code.RuntimeMonitorTest.at2:33
|_code.RuntimeMonitorTest$A.a1:50 耗时：2.62 ms
|__code.RuntimeMonitorTest$A.a2:55 耗时：2.57 ms
|___code.T2.t2:10 耗时：2.46 ms
|code.RuntimeMonitorTest.at2:34
|_code.RuntimeMonitorTest$C.c1:71 耗时：2.87 ms
|__code.T2.t2:10 耗时：2.69 ms
code.RuntimeMonitorTest.at2 耗时：42.2ms
-----------------------------------end-------------------------------------
```

## 特别声明
本监控程序只是通过assist字节码增强方案，做到对方法添加耗时统计，<br>
在close方法里面 只会记录耗时大于当前栈的调用耗时大于1ms的日志，<br>
否者当你循环等特别多的时候，会特别特别耗时；
请在调试模式下使用消耗统计，因为统计收集本身也就特别耗时；
