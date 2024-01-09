# org.wxd.boot.assist

## 介绍
通过assist字节码增强做到对项目调用路由链的耗时监控

## 软件架构
软件架构说明

通过启动参数增加
-javaagent:..\target\libs\assist.jar=需要监控的包名
jar包的路径位置和需要你监控的包名。多个包名可以用 “|” 进行切割

示例代码在 src/test/java/code/RuntimeMonitorTest

需要监控的类可以通过实现 IAssistMonitor 接口来增强监控的实现，可以重新里面方法

IAssistOutFile 接口的实现可以达到对增强后字节码文件输出，方便你查看增强后字节码文件内容

输出目录target/assist-out/

