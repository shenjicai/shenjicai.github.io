---
layout: post
title:  "STM32开发方式"
tags:
  - stm32
  - mcu
hero: https://source.unsplash.com/collection/430471/
overlay: blue
published: ture

---

# STM32开发方式及工程模板创建



在51单片机的程序开发中，采用直接配置51单片机的寄存器来控制芯片的工作方式，例如中断、定时器等。配置的时候往往需要查阅寄存器表，查看用到的是哪个寄存器以及完成相应功能是置0还是置1，这些工作较为**琐碎、机械，浪费大量的时间**。而且51单片机软件相对比较简单，资源很有限，可以采用这种直接配置寄存器的方式；
{: .lead} 

但是STM32功能比较强大，在嵌入式领域处于低端和高端之间，外设资源丰富，带来的必然是寄存器的数量和复杂度的增加，
<!–-break-–> 
如果继续采用**直接配置寄存器**的方式会有开发速度慢、程序可读性差的缺点，直接影响到开发效率、程序维护成本和交流成本，这时采用库开发方式，就显得十分有必要了。如图所示，STM32有两种开发方式[\[1\]](#ref_3)。

![](https://pic3.zhimg.com/v2-816b884e044a940000b538fa68a784f6_r.jpg)

STM32库是由ST公司针对STM32提供的函数接口，是架设在寄存器与用户驱动层之间的代码，向下处理与寄存器直接相关的配置，向上为用户提供配置寄存器的接口。开发者可调用这些函数接口来配置STM32的寄存器，使开发人员得以解脱最底层的寄存器操作，有开发快速、易于阅读、维护成本低等优点。

因为基于Cortex的某系列芯片采用的内核是相同的，区别主要是内核外的片上外设的差异，这些差异导致软件在同内核、不同外设的芯片上移植困难。为了解决_不同的芯片厂商生产的Cortex控制器软件的兼容性问题_，ARM与芯片厂商建立了**CMSIS标准（Cortex MicroController Software Interface Standard）**。CMSIS是ARM公司与多家不同的芯片和软件供应商一起紧密合作定义的，提供了内核与外设、实时操作系统和中间设备之间的**通用接口**。

![](https://pic2.zhimg.com/v2-d46d26e8a7f9bdfac6657ffb2b92885d_r.jpg)

STM32上市后迅速占领了中低端MCU市场，与它倡导的基于固件库的升发方式密不可分。采用库开发的方式可以快速上手，仅通过调用库里面的API函数就可以迅速搭建一个大型的程序，写出各种用户所需的应用，这大大降低了学习的门槛和开发周期。

但不管有多高级的处理器，**归根结底**都是要对**处理器的寄存器进行操作**。寄存器编程原理：根据芯片厂家给出的存储器映射可以看出各个外设的基地址，基地址加上相应的偏移量就是寄存器的地址，之后修改寄存器的内容就可以得到对寄存器的控制。**寄存器编程的关键**是如何构造好的数据结构对寄存器进行操作。STM32的固件库不是万能的，库函数固然好用，最初的寄存器操作也有其特殊用途。

![](https://pic3.zhimg.com/v2-47f16d0b427836deb2e8994c46827ce2_r.jpg)

对比STM32的两种开发方式：库函数操作简单，但是效率不如寄存器操作高；寄存器操作要熟悉上百个寄存器，很复杂，但是程序效率很高。因此，我们要将库函数编程为主要开发方式，但首先要理解重要寄存器的使用。当然，虽然学习固件库不需要记住每个寄存器的作用，但是通过寄存器操作可以对外设的一些功能有所了解，这样对库函数的学习和使用也很有帮助。

库开发
---

为了减轻STM32微控制器程序设计人员的编程负担，提高编程效率，意法半导体公司（ST）组织技术人员按照CMSIS标准为STM32微控制器中各个外设（包括核内外设和核外外设）的操作，编写了比较规范和完备的C语言标准外设驱动函数在使用STM32进行程序设计时，如果要对外设进行配置利操作，只需按照函数使用说明，调用这些外设的标准驱动函数即可，并不需要深入了解这些函数在代码层面的具体实现细节这些驱动函数按照不同外设的分类编排在不同的C语言文件中，并对应有各自的头文件，这些文件的集合就构成了STM32的**标准外设库（也称固件库）(SPL 库)、HAL 库、LL 库**。前两者都是常用的库，后面的 LL 库是 ST 最近才添加，随 HAL 源码包一起提供，目前支持的芯片也偏少[\[2\]]。各库如下所示：

![](https://pic3.zhimg.com/v2-dd425f61ce151a5851f5788ad9febd02_r.jpg)

其中，SPL 库 和 HAL 库两者相互独立，互不兼容。几种库的比较如下[\[3\]](#ref_3)：

![](https://pic3.zhimg.com/v2-88b6df7ad0a3874b55f19d51805bb30e_r.jpg)

目前几种库对不同芯片的支持情况如下[\[4\]](#ref_4)：

![](https://pic3.zhimg.com/v2-0d6d0806794aa9778ab71664f610dcc6_r.jpg)

ST 中文官网上有一篇《关于ST库函数的代码性能对比》的文章，其中对比了各种库的性能如下：

![](https://pic1.zhimg.com/v2-302775ce495e1d00d787ebf23a23db78_r.jpg)

### SPL库

标准外设库（**Standard Peripherals Library**）是对 STM32 芯片的一个完整的封装，包括所有标准器件外设的器件驱动器。这应该是目前使用最多的 ST 库。几乎全部使用 C 语言实现。但是，标准外设库也是针对某一系列芯片而言的，没有可移植性[\[5\]](#ref_5)。

![](https://pic1.zhimg.com/v2-2d84b23339534518d65143bd5f9a39e4_r.jpg)

STM32标准外设库历经2.0版本、3.2版本，直到现在的**[3.6版本](https://link.zhihu.com/?target=https%3A//www.st.com/content/st_com/en/products/embedded-software/mcu-mpu-embedded-software/stm32-embedded-software/stm32-standard-peripheral-libraries/stsw-stm32054.html%23get-software)**。虽然ST公司也推出了更为抽象化的**HAL库**但是目前占据市场统治地位的STM32F10x和STM32F40x系列微控制器芯片的编程仍然以采用标准外设库为主流。既然STM32标准外设库是按照CMSIS标准编写的，具体如图所示[\[6\]](#ref_6)。

![](https://pic4.zhimg.com/v2-d19d9498d781975d3d3f3f1693663e67_r.jpg)

相对于 HAL 库，标准外设库仍然接近于寄存器操作，主要就是将一些基本的寄存器操作封装成了 C 函数。开发者需要关注所使用的外设是在哪个总线之上，具体寄存器的配置等底层信息。

对于 STM32 Snippets，标准外设库对各外设的进行了一次封装，而不是仅仅局限在对寄存器的封装。实现了各外设的基本操作接口。

标准外设库的文件基本架构并不复杂。下图显示了 STM32F10xx 标准外设库文件的基本架构[\[7\]](#ref_7)。

![](https://pic1.zhimg.com/v2-4c576152fef474069bc44cf88f76cfa8_r.jpg)

其他系列的库文件结构和上图基本都是一致的。ST 为各系列提供的标准外设库稍微有些区别。例如，STM32F1x 的库和 STM32F3x 的库在文件结构上就有些不同，此外，在内部的实现上也稍微有些区别，这个在具体使用（移植）时，需要注意一下！但是，不同系列之间的差别并不是很大，而且在设计上是相同的。STM32 的标准外设库涵盖以下 3 个抽象级别：

*   包含位，位域和寄存器在内的完整的寄存器地址映射；
*   涵盖所有外围功能（具有公共API的驱动器）的例程和数据结构的集合；
*   一组包含所有可用外设的示例，其中包含最常用的开发工具的模板项目。

关于更详细的信息，可以参考 ST 的官方文档，文档中对于标准外设库函数命名、文件结构等都有详细的说明。

**实际操作**

新建文件夹[\[8\]](#ref_8)

![SPL_Lib_Proj_folder](../resources/SPL_Lib_Proj_folder.png)

拷贝固件库文件到我们创建的工程

![SPL_Lib_Proj_folder1](../resources/SPL_Lib_Proj_folder1.png)

文件夹目录
![SPL_Lib_Proj_folder2](../resources/SPL_Lib_Proj_folder2.png)

将文件分组添加到Keil工程目录中

![](https://pic1.zhimg.com/v2-71f90eee00a726afc0d4ef0b2ff6f6d8_r.jpg)

配置工程

![](https://pic1.zhimg.com/v2-38db2e0c52e9c8e64026fb881ec9dd24_r.jpg)

![](https://pic3.zhimg.com/v2-4befbb24dd6c76a6138836cb4894df9a_r.jpg)

![](https://pic4.zhimg.com/v2-89020b7b07c21a8208cb40724f777a8f_r.jpg)

![](https://pic2.zhimg.com/v2-f8483eddf0825521f187acc388e67341_r.jpg)

### STMCube

ST 为新的标准库注册了一个新商标：**STMCube**[\[9\]](#ref_9)。并且，ST专门为其开发了配套的桌面软件 STMCubeMX，开发者可以直接使用该软件进行可视化配置，大大节省开发时间。

![](https://pic4.zhimg.com/v2-8209797fb63b0b828fd675152d60af83_r.jpg)

每种STM32Cube软件工具都有一个独立的版本。

来自授权合作伙伴或第三方（包括IAR EWARM或Keil MDK-ARM IDE）的STM32兼容工具可以无缝集成到STM32Cube软件套件中并因此而获益。 开发人员可以继续使用他们熟悉的工具，同时利用STM32Cube软件工具的丰富特性。

这其中就包含了 HAL 库和最近新增的 LL 库[\[10\]](#ref_10)。如下图：

![](https://pic2.zhimg.com/v2-a5d90c6c5db023d4f4548855eef3631d_r.jpg)

从上图不难看出，LL 库和 HAL 库两者相互独立，只不过LL库更底层。而且，部分 HAL 库会调用LL库（例如：USB驱动）。同样，LL 库也会调用 HAL 库

用户可以使用 STMCubeMX 直接生成对应芯片的整个项目（目前主流开发工具的项目基本全支持），STMCubeMX 负责给整理各种需要的源码文件。

注意：

1.  个人感觉STMCubeMX生成的项目并不够简洁，源码的组织结构也并不是很好；
2.  STMCubeMX在生产项目时，可以选择使用HAL库或者LL库。但是部分组件的HAL库会调用LL库。

### HAL 库

HAL是 **Hardware Abstraction Layer** 的缩写，中文名：**硬件抽象层**。HAL 库是 ST 为 STM32 最新推出的抽象层嵌入式软件，可以更好的确保跨 STM32 产品的最大可移植性。该库提供了一整套一致的中间件组件，如 RTOS，USB，TCP/IP 和 图形等。

HAL 库是基于一个非限制性的 BSD 许可协议（Berkeley Software Distribution）而发布的开源代码。 ST 制作的中间件堆栈（USB 主机和设备库，STemWin）带有允许轻松重用的许可模式， 只要是在 ST 公司的 MCU 芯片上使用，库中的中间件(USB 主机/设备库,STemWin)协议栈即被允许随便修改，并可以反复使用。至于基于其它著名的开源解决方案商的中间件（FreeRTOS，FatFs，LwIP和PolarSSL）也都具有友好的用户许可条款。

作为目前 ST 主推的外设库，HAL库相关的文档还是非常详细的。

![](https://pic1.zhimg.com/v2-fdc205808c4f025934d6642638502050_r.jpg)

可以说HAL 库就是用来取代之前的标准外设库的。相比标准外设库，STM32Cube HAL 库表现出更高的抽象整合水平，HAL API 集中关注各外设的公共函数功能，这样便于定义一套通用的用户友好的API函数接口，从而可以轻松实现从一个STM32产品移植到另一个不同的STM32系列产品。HAL库是ST未来主推的库，从前年开始ST新出的芯片已经没有STD库了，比如 F7 系列。目前，HAL库已经支持STM32全线产品。

使用HAL库编程，最好尽量符合HAL库编程的整体架构。

更详细的介绍阅读：[HAL库详解及手动移植](https://link.zhihu.com/?target=https%3A//blog.csdn.net/zcshoucsdn/article/details/55213616)

### LL 库

LL库（**Low Layer**）是 ST 最近新增的库，与 HAL 库捆绑发布，文档也是和 HAL 库文档在一起的，比如：在STM32F3x 的 HAL 库说明文档中，ST 新增了LL库这一章节，但是在 F2x 的HAL文档中就没有。  
LL 库更接近硬件层，对需要复杂上层协议栈的外设不适用，直接操作寄存器。其支持所有外设。使用方法：

*   独立使用，该库完全独立实现，可以完全抛开 HAL 库，只用LL库编程完成。在使用STM32CubeMX生成项目时，直接选LL库即可。如果使用了复杂的外设，例如 USB，则会调用 HAL 库
*   混合使用，和 HAL 库结合使用。

目前，CubeMX 在生成项目时，可以选择采用 LL 库。  
LL 库文件的命名方式和 HAL 库基本相同。个人感觉，LL 库就是原来的标准外设库移植到 Cube下的新的实现，但是其实现方法更加高效、简洁。使用 LL 库编程和使用标准外设库的方式基本一样，但是确会得到比标准外设库更高的效率。  
更详细的介绍阅读：[LL 库（low-layer drivers）详解及移植说明](https://link.zhihu.com/?target=https%3A//itexp.blog.csdn.net/article/details/104174662)



参考
--

1.  [屈微，王志良. STM32单片机应用基础与项目实践 微课版. 北京：清华大学出版社, 2019.06.](http://book.ucdrs.superlib.net/views/specific/2929/bookDetail.jsp?dxNumber=000018186474&d=76E8BF3F036C54E5CA669EAC8E204919&fenlei=181704080101)
2.  [STM32 Embedded Software](https://blog.csdn.net/ZCShouCSDN/article/details/54613202)
3.  [STM32 之HAL库、标准外设库、LL库](https://www.its404.com/article/weixin_45905650/108391441)
4.  [STM32 Embedded Software overview](https://www.st.com/content/ccc/resource/sales_and_marketing/presentation/product_presentation/37/55/ff/bc/a8/71/4f/c5/stm32_embedded_software_offering.pdf/files/stm32_embedded_software_offering.pdf/jcr:content/translations/en.stm32_embedded_software_offering.pdf)
5.  [STM32标准外设软件库](https://www.st.com/zh/embedded-software/stm32-standard-peripheral-libraries.html#)
6.  [基于STM32的嵌入式系统应用 2019](http://book.ucdrs.superlib.net/views/specific/2929/bookDetail.jsp?dxNumber=000018622203&d=F83D9D30ECE2BFDAE7E8402964D30CA9&fenlei=181704050203)
7.  [How to migrate from the STM32F10xxx firmware library V2.0.3 to the STM32F10xxx standard peripheral library V3.0.0](https://www.st.com/resource/en/application_note/an2953-how-to-migrate-from-the-stm32f10xxx-firmware-library-v203-to-the-stm32f10xxx-standard-peripheral-library-v300-stmicroelectronics.pdf)
8.  [hezhijie157——STM32入门编程手把手](https://www.bilibili.com/video/BV1kE411Q74i?spm_id_from=333.999.0.0)
9.  [STM32Cube Ecosystemml](https://www.st.com/content/st_com/zh/ecosystems/stm32cube-ecosystem.html)
10. [STM32嵌入式软件概述](https://www.st.com/content/ccc/resource/sales_and_marketing/presentation/product_presentation/37/55/ff/bc/a8/71/4f/c5/stm32_embedded_software_offering.pdf/files/stm32_embedded_software_offering.pdf/jcr:content/translations/en.stm32_embedded_software_offering.pdf)