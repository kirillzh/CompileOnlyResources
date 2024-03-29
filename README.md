In this example we have Android `app` module that depends Android library `feature` module.
`feature-robots` Android library module only contains UI instrumentation helpers for UI testing `feature`, specifically it references one of the resources from `feature`.
`app` has UI test that depends on `feature-robots` in order to test `feature`.
Note that `feature-robots` depends on `feature` in compile time only using `compileOnly` Gradle configuration. That's because target and instrumentation APKs run in the same JVM so they have the same classpath which makes it unnecessary to ship the app code into the instrumentation apk.
This however doesn't work because `compileOnly` does not seem to provide Android resources, only jar with classes, whereas `implementation` does both + packages them.
Ultimately, we want to keep our robot modules (UI helpers) as thin as possible.

[AGP bug: compileOnly configuration doesn't propagate R class](https://issuetracker.google.com/issues/144444371)

```
java.lang.NoClassDefFoundError: Failed resolution of: Lcom/kirillzh/featurerobots/R$string;
at com.kirillzh.featurerobots.FeatureRobot.clickSomething(FeatureRobot.java:5)
at com.kirillzh.ExampleTest.testSomething(ExampleTest.java:11)
at java.lang.reflect.Method.invoke(Native Method)
at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
at org.junit.runners.Suite.runChild(Suite.java:128)
at org.junit.runners.Suite.runChild(Suite.java:27)
at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
at org.junit.runner.JUnitCore.run(JUnitCore.java:115)
at androidx.test.internal.runner.TestExecutor.execute(TestExecutor.java:56)
at androidx.test.runner.AndroidJUnitRunner.onStart(AndroidJUnitRunner.java:388)
at android.app.Instrumentation$InstrumentationThread.run(Instrumentation.java:2145)
Caused by: java.lang.ClassNotFoundException: Didn't find class "com.kirillzh.featurerobots.R$string" on path: DexPathList[[zip file "/system/framework/android.test.runner.jar", zip file "/system/framework/android.test.mock.jar", zip file "/data/app/com.kirillzh.test-hCYsJlL3_Ly8iceHF9eEFw==/base.apk", zip file "/data/app/com.kirillzh-APjSsSOmbffB2fCDHiOstQ==/base.apk"],nativeLibraryDirectories=[/data/app/com.kirillzh.test-hCYsJlL3_Ly8iceHF9eEFw==/lib/x86, /data/app/com.kirillzh-APjSsSOmbffB2fCDHiOstQ==/lib/x86, /system/lib]]
at dalvik.system.BaseDexClassLoader.findClass(BaseDexClassLoader.java:134)
at java.lang.ClassLoader.loadClass(ClassLoader.java:379)
at java.lang.ClassLoader.loadClass(ClassLoader.java:312)
... 29 more

E/TestRunner: java.lang.NoClassDefFoundError: Failed resolution of: Lcom/kirillzh/featurerobots/R$string;
        at com.kirillzh.featurerobots.FeatureRobot.clickSomething(FeatureRobot.java:5)
        at com.kirillzh.ExampleTest.testSomething(ExampleTest.java:11)
        at java.lang.reflect.Method.invoke(Native Method)
        at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
        at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
        at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
        at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
        at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
        at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
        at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
        at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
        at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
        at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
        at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
        at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
        at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
        at org.junit.runners.Suite.runChild(Suite.java:128)
        at org.junit.runners.Suite.runChild(Suite.java:27)
        at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
        at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
        at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
        at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
        at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
        at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
        at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
        at org.junit.runner.JUnitCore.run(JUnitCore.java:115)
        at androidx.test.internal.runner.TestExecutor.execute(TestExecutor.java:56)
        at androidx.test.runner.AndroidJUnitRunner.onStart(AndroidJUnitRunner.java:388)
        at android.app.Instrumentation$InstrumentationThread.run(Instrumentation.java:2145)
     Caused by: java.lang.ClassNotFoundException: Didn't find class "com.kirillzh.featurerobots.R$string" on path: DexPathList[[zip file "/system/framework/android.test.runner.jar", zip file "/system/framework/android.test.mock.jar", zip file "/data/app/com.kirillzh.test-hCYsJlL3_Ly8iceHF9eEFw==/base.apk", zip file "/data/app/com.kirillzh-APjSsSOmbffB2fCDHiOstQ==/base.apk"],nativeLibraryDirectories=[/data/app/com.kirillzh.test-hCYsJlL3_Ly8iceHF9eEFw==/lib/x86, /data/app/com.kirillzh-APjSsSOmbffB2fCDHiOstQ==/lib/x86, /system/lib]]
 
 ```