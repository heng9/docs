# Intellj idea 快捷方式

* **File**
	* **Setting**					ctrl + alt + S
	* **Project Structure** 		ctrl + alt + shift + S
		* **Spring 关联**


* **Edit**
	* **copy refrence**					ctrl + shift + alt + C
	* **complete current statement**	ctrl + shift + enter		自动完成
	* **show intention actions**		alt + enter					代码提示
	* **toggle case**					ctrl + shift + U
	* **find**
		* **find** 						ctrl + F
		* **find in path** 				ctrl + shift + F 	查找字符串
		* **replace**					ctrl + R


* **View**
	* **tool windows**		
		* **project**			alt + 1
		* **Debug** 			alt + 5
		* **terminal**			alt + F12
		* **database**		
			* **数据库关联**  =>  Make Global 对数据库进行全局关联
	* **jump to source**		F4 / alt + 左键(类/方法)


* **Navigate**
	* **Class**					ctrl + N 					查找类
	* **File**					ctrl + shift + N 			查找文件
	* **Symbol**				ctrl + shift + alt + N 		查找方法
	* **custom folding**		ctrl + shift + .			折叠代码段 <br/>
								ctrl + "+/-"				折叠类或方法 <br/>
								ctrl + shift + "+/-"		折叠所有方法
	* **back**					alt + shift + ←
	* **forwar**				alt + shift + →
	* **super class**			ctrl + U
	* **file structure**		ctrl + F12					查看类的继承关系
	* **type hierarchy**		ctrl + H 					查看类结构
	* **call hierarchy**		ctrl + alt + H 				查看方法调用层次
	* **Find Usages**			ALT + F7    				查看类被引用
	* **next hightlighted error** 		F2
	* **previous hightlighted error** 	shift + F12


* **Code**
	* **override method**					ctrl + O
	* **implement method** 					ctrl + I
	* **generate**							alt + insert 			代码生成
	* **completion**
		* **cyclic expand word**			alt + /
	* **comment with line comment**			ctrl + /				
	* **comment with block comment** 		ctrl + shift + /		
	* **reformat code**						ctrl + alt + L 			


* **Refactor**
	* **rename**			shift + F6


* **Help**
	* **find action**		ctrl + shift + A


* **Debug**
	* **step into**				F7					进入方法
	* **step over**				F8					单步调试
	* **Resume Program**		F9					下一断点
								ctrl + F2 			退出调试
								alt + F9			运行到光标指定行
	* **View Breakpoints**		ctrl + shift + F8	查看所有断点
	* **Mute Breakpoints**							禁止所有断点
								断点处右键			条件断点
	* **Evaluate Express**		alt + F8			表达式求值
	* **Set Value**
	* **Add to Watches**			


* **Other**
	* **ctrl + shift + alt + U** 		查看类结构图
	* **ctrl + alt + 左键(类)**			查找子类


* **Live Template**
	* **psvm**	public static void main(String[] args){}
	* **sout**	System.out.println()
	
	
* **Postfix Completion**
	* **for**
	* **nn**
	* **field**
	* **format**


* **实用插件**
	* **Lombok**
	* **mybatis plugin**
	* **Background Image Plus**
	* **CodeGlance**


* **其他**
	* @Autowired取消提示 <br/>
		Inspections  =>  Spring  =>  Spring Core  =>  Code  =>  Autowaring for Bean Class
		
	* Mybatis取消提示
		* 去掉 No data sources configure 警告 <br/>
		Prefernces ⇒ Editor ⇒ Inspections ⇒ SQL ⇒ No data sources configure
		* 去掉 SQL dialect is not configured 警告 <br/>
		Prefernces ⇒ Editor ⇒ Inspections ⇒ SQL ⇒ SQL dialect detection
		* 去掉“注入语言”的背景色
		Prefernces ⇒ Editor ⇒ Colors & Fonts ⇒ General ⇒ Code ⇒ Injected language fragment ⇒ Background	
    
    
    Ctrl + Shift + F9  编译
