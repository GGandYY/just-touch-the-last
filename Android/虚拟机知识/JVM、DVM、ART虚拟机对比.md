#JVM#
##概述##
**JVM本质上就是一个软件**，是计算机硬件的一层软件抽象，在这之上才能够运行Java程序，JAVA在编译后会生成类似于汇编语言的JVM字节码，与C语言编译后产生的汇编语言不同的是，**C编译成的汇编语言会直接在硬件上跑，但JAVA编译后生成的字节码是在JVM上跑**，需要由JVM把字节码翻译成机器指令，才能使JAVA程序跑起来。

JVM运行在操作系统上，屏蔽了底层实现的差异，从而有了JAVA吹嘘的平台独立性和Write Once Run Anywhere。根据JVM规范实现的具体虚拟机有几十种，主流的JVM包括Hotspot、Jikes RVM等，都是用C/C++和汇编编写的，每个JRE编译的时候针对每个平台编译，因此下载JRE（JVM、Java核心类库和支持文件）的时候是分平台的，**JVM的作用是把平台无关的.class里面的字节码翻译成平台相关的机器码，来实现跨平台**。

#DVM#
##概述##
JVM是Java Virtual Machine，而DVM就是Dalvik Virtual Machine，是安卓中使用的虚拟机，所有安卓程序都运行在安卓系统进程里，每个进程对应着一个Dalvik虚拟机实例。**他们都提供了对象生命周期管理、堆栈管理、线程管理、安全和异常管理以及垃圾回收等重要功能，各自拥有一套完整的指令系统**，以下简要对比两种虚拟机的不同：

- **JAVA虚拟机运行的是JAVA字节码，Dalvik虚拟机运行的是Dalvik字节码**

	JAVA程序经过编译，生成JAVA字节码保存在class文件中，JVM通过解码class文件中的内容来运行程序。而DVM
	运行的是Dalvik字节码，所有的Dalvik字节码由JAVA字节码转换而来，并被打包到一个DEX（Dalvik Executable）可执行文件中，DVM通过解释DEX文件来执行这些字节码。

- **Dalvik可执行文件体积更小**

	以下是JVM规范中以C的数据结构表达的class文件结构，class文件被虚拟机加载到内存中后便是这样
![](http://img.blog.csdn.net/20151231114022450?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

	class文件中包含多个不同的方法签名，如果A类文件引用B类文件中的方法，方法签名也会被复制到A类文件中（在虚拟机加载类的连接阶段将会使用该签名链接到B类的对应方法），也就是说，多个不同的类会同时包含相同的方法签名，同样地，大量的字符串常量在多个类文件中也被重复使用，这些冗余信息会直接增加文件的体积，而JVM在把描述类的数据从class文件加载到内存时，需要对数据进行校验、转换解析和初始化，最终才形成可以被虚拟机直接使用的JAVA类型，因为大量的冗余信息，会严重影响虚拟机解析文件的效率。

	为了减小执行文件的体积，安卓使用Dalvik虚拟机，SDK中有个dx工具负责将JAVA字节码转换为Dalvik字节码，dx工具对JAVA类文件重新排列，将所有JAVA类文件中的常量池分解，消除其中的冗余信息，重新组合形成一个常量池，所有的类文件共享同一个常量池，使得相同的字符串、常量在DEX文件中只出现一次，从而减小了文件的体积。
	dx工具的转换过程和DEX文件的结构如下图所示：
![](http://img.blog.csdn.net/20151231114039244?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

- **JVM基于栈，DVM基于寄存器**

	JAVA虚拟机基于栈结构，程序在运行时虚拟机需要频繁的从栈上读取写入数据，这个过程需要更多的指令分派与内存访问次数，会耗费很多CPU时间。

	Dalvik虚拟机基于寄存器架构，数据的访问通过寄存器间直接传递，这样的访问方式比基于栈方式要快很多。

#ART虚拟机#
##概述##
首先了解JIT（Just In Time，即时编译技术）和AOT(Ahead Of Time，预编译技术)两种编译模式。

JIT以JVM为例，javac把程序源码编译成JAVA字节码，JVM通过逐条解释字节码将其翻译成对应的机器指令，逐条读入，逐条解释翻译，执行速度必然比C/C++编译后的可执行二进制字节码程序慢，为了提高执行速度，就引入了JIT技术，JIT会在运行时分析应用程序的代码，识别哪些方法可以归类为热方法，这些方法会被JIT编译器编译成对应的汇编代码，然后存储到代码缓存中，以后调用这些方法时就不用解释执行了，可以直接使用代码缓存中已编译好的汇编代码。这能显著提升应用程序的执行效率。（安卓Dalvik虚拟机在2.2中增加了JIT）

相对的AOT就是指C/C++这类语言，编译器在编译时直接将程序源码编译成目标机器码，运行时直接运行机器码。

**Dalvik虚拟机执行的是dex字节码，ART虚拟机执行的是本地机器码**

Dalvik执行的是dex字节码，依靠JIT编译器去解释执行，运行时动态地将执行频率很高的dex字节码翻译成本地机器码，然后在执行，但是将dex字节码翻译成本地机器码是发生在应用程序的运行过程中，并且应用程序每一次重新运行的时候，都要重新做这个翻译工作，因此，即使采用了JIT，Dalvik虚拟机的总体性能还是不能与直接执行本地机器码的ART虚拟机相比。

安卓运行时从Dalvik虚拟机替换成ART虚拟机，并不要求开发者重新将自己的应用直接编译成目标机器码，也就是说，应用程序仍然是一个包含dex字节码的apk文件。所以在安装应用的时候，dex中的字节码将被编译成本地机器码，之后每次打开应用，执行的都是本地机器码。移除了运行时的解释执行，效率更高，启动更快。（**安卓在4.4中发布了ART运行时**）

##优缺点##
- ART优点：
	- 系统性能显著提升
	- 应用启动更快、运行更快、体验更流畅、触感反馈更及时
	- 续航能力提升
	- 支持更低配置的硬件

- ART缺点
	- 更大的存储空间占用，可能增加10%-20%
	- 更长的应用安装时间

**总的来说ART就是“空间换时间”**

