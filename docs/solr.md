# Window7 安装solr
> Solr是一个基于Lucene的Java搜索引擎服务器。Solr 提供了层面搜索、命中醒目显示并且支持多种输出格式（包括 XML/XSLT 和 JSON 格式）。它易于安装和配置，而且附带了一个基于 HTTP 的管理界面。Solr已经在众多大型的网站中使用，较为成熟和稳定。Solr 包装并扩展了 Lucene，所以Solr的基本上沿用了Lucene的相关术语。更重要的是，Solr 创建的索引与 Lucene 搜索引擎库完全兼容。通过对 Solr 进行适当的配置，某些情况下可能需要进行编码，Solr 可以阅读和使用构建到其他 Lucene 应用程序中的索引。此外，很多 Lucene 工具（如Nutch、 Luke）也可以使用 Solr 创建的索引。

1. **准备工作**
	* jdk-1.8
	* tomcat-8
	* solr-7.2.1

2. **搭建步骤**：
	* 把solr-7.2.1.zip解压之后，找到solr-7.2.1/server/server-webapp/目录下的webapp文件夹，复制粘贴到tomcat下的webapps下；并改名为solr。
	
	* 把solr-7.2.1/server/lib/ext目录下的所有jar包
		solr-7.2.1/dist下的  
			solr-dataimporthandler-6.4.1.jar  
			solr-dataimporthandler-extras-6.4.1.jar  
		solr-7.2.1/server/lib下的  
			metrics-core-3.1.2.jar  
			metrics-ganglia-3.1.2.jar  
			metrics-graphite-3.1.2.jar  
			metrics-jetty9-3.1.2.jar  
			metrics-jvm-3.1.2.jar  
		粘贴复制到tomcat下的solr项目的WEB-INF/lib目录下。

	* 在tomcat下的solr项目的WEB-INF下新建classes目录，然后把solr-7.2.1/server/resources下的log4j.properties粘贴过去。

	* 把solr-7.2.1/server目录下的solr文件夹粘贴复制到其他目录，然后重命名为：solr_home，这是solr核心文件夹。

	* 修改tomcat下的solr项目的WEB-INF/web.xml，这一步用来指定默认的solr_home。在web.xml开头附近找到注释语句：
	```
	<env-entry>
		<env-entry-name>solr/home</env-entry-name>
		<env-entry-value>D:/solrhome</env-entry-value>
		<env-entry-type>java.lang.String</env-entry-type>
	</env-entry>
	```
		然后取消注释，把<env-entry-value>填第三步solr_home文件夹的地址。

	* 把tomcat下的solr项目的web.xml文件中
		```
		<security-constraint>
			<web-resource-collection>
				<web-resource-name>Disable TRACE</web-resource-name>
				<url-pattern>/</url-pattern>
				<http-method>TRACE</http-method>
			</web-resource-collection>
			<auth-constraint/>
		</security-constraint>
		<security-constraint>
			<web-resource-collection>
				<web-resource-name>Enable everything but TRACE</web-resource-name>
				<url-pattern>/</url-pattern>
				<http-method-omission>TRACE</http-method-omission>
			</web-resource-collection>
		</security-constraint>
		```
		这段注释掉。这段配置限制了对solr资源的访问，注释掉就可以访问了。

	* 配置完成后，启动tomcat：访问 http://localhost:8080/solr/index.html

3. **Solr 添加 core**
	* 在 solr_home 下面新建一个文件夹, 名字任取, 这里取名my_core

	* 将 solr-7.2.1\server\solr\configsets\_default 下面的conf文件夹复制到my_core下面

	* 修改my_core/confg 下的solrconfig.xml文件，将下面的注释
		```
		<lib dir="${solr.install.dir:../../..}/contrib/extraction/lib" regex=".*\.jar" />
		<lib dir="${solr.install.dir:../../..}/dist/" regex="solr-cell-\d.*\.jar" />

		<lib dir="${solr.install.dir:../../..}/contrib/clustering/lib/" regex=".*\.jar" />
		<lib dir="${solr.install.dir:../../..}/dist/" regex="solr-clustering-\d.*\.jar" />

		<lib dir="${solr.install.dir:../../..}/contrib/langid/lib/" regex=".*\.jar" />
		<lib dir="${solr.install.dir:../../..}/dist/" regex="solr-langid-\d.*\.jar" />

		<lib dir="${solr.install.dir:../../..}/contrib/velocity/lib" regex=".*\.jar" />
		<lib dir="${solr.install.dir:../../..}/dist/" regex="solr-velocity-\d.*\.jar" />
		```
		中的../../.. 替换为 solr-7.2.1 目录位置

	* 打开solr的web界面添加core，instanceDir必须与文件夹名字一样
	
	* 注意：managed-schema 中的Field一定要包含id，且类型为string
		```
		<field name="id" type="string" multiValued="false" indexed="true" required="true" stored="true"/>
		```

4. **配置IKAnalyzer**
	* 文件介绍	
		* ext.dic - 扩展字典
		* stopword.dic - 停止词字典
		* IKAnalyzer.cfg.xml - 配置文件
		* solr-analyzer-ik-5.1.0.jar、ik-analyzer-solr5-5.x.jar 为分词jar包。

	* 将IK分词器 JAR 包拷贝到 Tomcat solr项目 WEB-INF\lib下
	
	* 将词典 配置文件拷贝到 Tomcat solr项目 WEB-INF\classes下
	
	* 更改 my_core\conf 下的 managed-schema 配置文件，添加以下：
	```
	<fieldType name="text_ik" class="solr.TextField">
		<analyzer type="index">
			<tokenizer class="org.apache.lucene.analysis.ik.IKTokenizerFactory" useSmart="true"/>
		</analyzer>
		<analyzer type="query">
			<tokenizer class="org.apache.lucene.analysis.ik.IKTokenizerFactory" useSmart="true"/>
		</analyzer>
	</fieldType>
	```
	
	* 这样就可以使用IK分词器了：
	```
	<field name="content" type="text_ik" multiValued="false" indexed="true" stored="true"/>
	```