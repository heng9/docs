# SonarQube

* 单元测试
    * 在pom.xml中添加jacoco插件
    * 执行单元测试命令：mvn test
    * 结果在target目录下生产jacoco.exec文件，表明jacoco正确执行

或者
```
mvn clean package org.jacoco:jacoco-maven-plugin:prepare-agent -Dmaven.test.failure.ignore=true
```

sonar命令：
```
mvn clean install -Dmaven.test.skip=true
mvn sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.login=8ae1b77d378d57bc3751358352895ae866fe7534
```

* sonar覆盖率
    * 在pom.xml中添加jacoco插件
    * mvn clean test sonar:sonar -Dsonar.host.url=http://localhost:9000
    

* spring 热部署：
    1. dependency添加：spring-boot-devtools (devtools会监听classpath下的文件变动，并且会立即重启应用（发生在保存时机）)
    2. plugin添加：spring-boot-maven-plugin
    3. 在设置中打开自动编译：Compiler => Build project automatically
    4. 打开运行时编译：按快捷键 Shift+Ctrl+Alt+/, 选择 Registry, compiler.automake.allow.when.app.running 打钩  
    (3、4步骤可以使用快捷键达到重新编译的效果：Ctrl + Shift + F9)