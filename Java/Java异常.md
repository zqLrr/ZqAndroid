# Java异常

日常工作总结的Java异常

1. `Android android.os.DeadSystemException`

出现原因：被系统杀掉服务导致出现的原因，一般出现在后台用户无感知。

解决方案：暂时不处理，是否抛出异常是有mInstrumentation.onException(s, e)来判断的，是否可以hook住 Instrumentation。重写onException函数，判断不上报异常。