# Java
* 查看java版本
```
java -version
```

* 将java源文件编译成.class字节码文件
```
javac <MainClass.java>
```

* 运行字节码文件；由java虚拟机对字节码进行解释和运行
```
java <MainClass>
```

注意: 在包下的类(即类中使用了package)，在Java源文件的地方编译后，需要到最外层包的上一级目录下运行，而且类前面需要带包名，以.隔开
解释器会根据包的全限定名找到对应目录下的类，否则会出现(找不到或无法加载主类)的错误  

classpath是Java运行时环境搜索类和其他资源文件（比如jar\zip等资源）的路径
classpath: .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;