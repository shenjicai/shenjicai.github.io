---
layout: post
title:  "旋转编码器原理、选型及编码"
tags:
  - hardware
hero: https://source.unsplash.com/random
overlay: green
published: ture

---
# 旋转编码器原理、选型及编码


# 原理
旋转编码器（rotary encoder）也称为轴编码器，是将旋转的机械位移量转换为电气信号，对该信号进行处理后检测位置速度等信号的传感器。检测直线机械位移量的传感器称为线性编码器[1]。一般装设在旋转物体中垂直旋转轴的一面。
{: .lead}
旋转编码器用在许多需要精确旋转位置及速度的场合，如工业控制、机器人技术、专用镜头、电脑输入装置（如鼠标及轨迹球）等。
<!–-break-–> 

旋转编码器可分为**绝对型（absolute）**编码器及**增量型（incremental）**编码器两种。增量型编码器也称作相对型编码器（relative encoder），利用检测脉冲的方式来计算转速及位置，可输出有关旋转轴运动的信号，一般会由其他设备或电路进一步转换为速度、距离、每分钟转速或位置的信号。绝对型编码器会输出旋转轴的位置，可视为一种角度传感器。
> 分类[2]：
> - 以编码器工作原理可分为：**光电式**、**磁电式**和**触点电刷式**。
> - 以码盘刻孔方式不同分为：**增量式**和**绝对式**两类。


二者的主要区别在于码盘的结构和输出信号的形式不同。增量型编码器输出的是脉冲信号，而绝对编码器输出的是二进制的数值[10]。

## 绝对值型编码器
绝对型编码器将转轴的不同位置加以编号，再依目前转轴位置输出对应的编号，依构造主要可分为两种：**光学式**及**机械式**[3]。
绝对型编码器的特点是随时可以知道转轴的位置，有时也会将解角器视为是绝对型编码器。
## 增量型编码器
增量型编码器和绝对型编码器不同，当转轴旋转时，增量型编码器输出会随之变化，根据输出变化可以检测转轴的旋转量。绝对型编码器有针对转轴旋转的位置给予编号，转轴不动时根据其输出的信号可以求得其对应的位置，增量型编码器无此功能，无法在转轴不动时得到转轴旋转位置的信息。

增量型编码器可用来感应转轴旋转量的信号，再由程序转化旋转方向、位置及角度等信息，增量型编码器可以是线性的，也可以是旋转型。增量型编码器因为其低成本，以及其信号容易转换为运动相关的信号（例如速度）等特性，是最广为使用的编码器。

增量型编码器有**机械式**的及**光学式**的，机械式的编码器需要对信号处理抖动，一般用在消费性产品上的旋钮。例如大部分家用及车用的收音机就是用增量型编码器作为音量控制的旋钮，一般机械式编码器只适用在转速不高的应用场合。光学式的编码器则用在高速或是需要高精准度的场合。

增量型编码器有**两个主要输出**，分别称为A和B，两个输出是正交输出，相位差为90度。增量型编码器的单圈脉冲数（PPR）为其旋转一圈时会输出的方波数，如PPR为600表示旋转一圈时A和B都会输出600个方波，但先后顺序不同。光学式增量型编码器可以有较高的单圈脉冲数，例如 2500 到 10000。
以下是顺时针及逆时针旋转时，编码器输出的变化：

| **逆时针旋转的输出** |  |  | **顺时针旋转的输出** |  |  |
| --- | --- | --- | --- | --- | --- |
| **Phase** | **A** | **B** | **Phase** | **A** | **B** |
| **1** | 0 | 0 | 1 | 1 | 0 |
| **2** | 0 | 1 | 2 | 1 | 1 |
| **3** | 1 | 1 | 3 | 0 | 1 |
| **4** | 1 | 0 | 4 | 0 | 0 |

二个信号有90度的**相位差**，在不同旋转方向时，二个信号的相序也有所不同，可以利用程序将两个信号进行解码。根据其相序不同，在有方波时使一计数器上数或是下数，此计数器的值即可对应转轴的旋转量。
例如上一次的数值是00，目前的数值是01，表示转轴已顺时针旋转了四分之一个单位（若单圈脉冲数为600，此处的单位即为六百分之一圈）。根据单位时间的旋转量可以计算转速，若是转速很慢时可以直接根据方波的宽度计算转速。若转轴的旋转速度太快，程序可能会跳过中间的状态变化，出现无法识别转轴的旋转方向或是旋转方向误判的情形。
> 当顺时针旋转时A信号提前B信号90度相位，当逆时针旋转时B信号提前A信号90度相位，电路接收到旋转编码器的A、B信号时，可以根据A、B的状态组合判定编码器的旋转方向。
> 程序设计中我们可以对A、B信号检测，检测A信号的边沿及B信号的状态，
> - 当A信号上升沿时B信号为低电平，或当A信号下降沿时B信号为高电平，证明当前编码器为顺时针转动
> - 当A信号上升沿时B信号为高电平，或当A信号下降沿时B信号为低电平，证明当前编码器为逆时针转动

有些旋转编码器除了**A相**及**B相**外还有一个输出，一般称为**Z相**，每旋转一圈Z相信号会有一个方波输出，可以用来判断转轴的绝对位置，例如用在位置控制的系统中。
若旋转编码器只有单独一相的输出，仍然可以判断转轴的转速，只是不能判断旋转的方向。可以用在量测转速的场合，有时也会以此量测运动的距离。
> 增量型编码器输出A相、B相和Z相分别代表的含义[4]：
> 编码器轴每旋转一圈，A相和B相都发出相同的脉冲个数，但是A相和B相之间存在一个90°（电气角的一周期为360°）的电气角相位差，可以根据这个相位差来判断编码器旋转的方向是正转还是反转，正转时，A相超前B相90°先进行相位输出，反转时，B相超前A相90°先进行相位输出（如下图所示）。编码器每旋转一圈，Z相只在一个固定的位置发一个脉冲，所以可以作为复位相或零位相来使用。
> ![img](https://picx.zhimg.com/80/v2-326b675909a1e780a195d95331c47147_1440w.png?source=d16d100b)


# 选型
在选型或采购旋转编码器的时候，需要从多方面进行考虑，特别是在**技术参数**上需要进行一个技术参数上的参考：包括编码器的**尺寸**、**类型**、**分辨率**、**电气接口**等等，总的来说，第一步则是判断应用需要的是增量编码器、绝对编码器还是换向编码器。一经确定，就必须考虑分辨率、安装方式、电机轴尺寸等其他参数[5]。（对磁场环境有要求的务必不能选择磁电技术的编码器！！！）
## 外形尺寸
 除了编码器的定位止口，轴径，安装孔位；安装空间体积等常规参数，还需考虑比如：安装空间与选定轴的形态（中空轴、杆轴类）：
![img](https://pic1.zhimg.com/80/v2-dcbba1101f2b770339ada00689cdff38_1440w.png?source=d16d100b "杆轴")![img](https://picx.zhimg.com/80/v2-183c06599955b3eb45c2ba8f1dcf10b0_1440w.png?source=d16d100b)
以及连接线缆类型：
![img](https://picx.zhimg.com/80/v2-37ba6bf543793602f9d2768982f26aaf_1440w.png?source=d16d100b)![img](https://picx.zhimg.com/80/v2-140d383d67900e50375960f37a3ab54b_1440w.png?source=d16d100b)
## 分解率精度
在考虑组装机械装置的要求精度和机械的成本的基础上，选择最适合的产品。一般选择机械综合精度的1/2～1/4精度的分辨率。
## 输出电路方式
对增量型编码器而言，其输出电路有很多类型，当使用高速计数器对编码器的脉冲信号进行计数时，必须首先搞清楚该编码器的输出类型才能正确的接线并调试。
增量型编码器的输出电路包括**集电极输出（Collector Output）型**、**电压输出（Voltage Output）型**、**推挽输出（Push-Pull Output）型**及**线驱动输出（Line Driver Output）**型。输出电路的核心元器件是三极管。我们知道三极管有三个极：基极（Base）、发射极（Emitter）和集电极（Collector）。

最合适的输出信号类型并不总是那么明显，而且往往受到忽视。最常见的三种类型是**开集输出**（电压输出-E）、**推挽输出**（F型HTL格式）和**差分线路驱动器输出**。本文将分别介绍这三种输出类型，帮助大家根据具体应用需求选择合适的设备。
### 首要原则
无论是增量编码器的正交输出，换向编码器的电机极输出，还是使用特定协议的串行输出，这些编码器输出都是数字信号。因此，5 V 编码器的信号会一直在近似 0 V 与 5 V 之间切换，这两个电压分别对应逻辑 0 和 1。增量编码器的输出是基本方波，如下图所示。
![img](https://pic3.zhimg.com/80/v2-516ee49652737dd9f42ac4eaea554c8c_1440w.png?source=d16d100b "数字编码器的通用方波输出")
### 开集输出（开路集电极输出）
集电极输出电路是以三极管的发射极为公共端，信号从集电极输出的电路。由于三极管分为PNP和NPN两种，相应的，编码器的集电极输出电路也分为PNP和NPN两种。
下面这张图是欧姆龙E6B2-CWZ5B编码器的集电极输出电路（PNP型），可以看到编码器的电源（+12V或+24V）通过一个3.3欧姆的电阻连接到三极管的发射极上，而脉冲信号（A/B/Z）则是从集电极进行输出：
![img](https://pic3.zhimg.com/80/v2-77cad9f3df6c03567bd8c66584920610_1440w.png?source=d16d100b "PNP型集电极输出电路")
NPN型集电极输出电路与之类似，只不过发射极（公共端）连接的是0V，如下面的E6B2-CWZ6C输出电路：
![img](https://pic1.zhimg.com/80/v2-24fe4b7640012ce00bda17dae4a089b6_1440w.png?source=d16d100b "NPN型集电极输出电路")

旋转编码器大多采用开集输出，即输入信号为高电平时，晶体管的集电极引脚保持开路或断开。当输出为低电平时，输出直接接地。
![img](https://pic3.zhimg.com/80/v2-f102ae3ab5616911af006dd7cf4328e4_1440w.png?source=d16d100b "开集输出原理图")
NPN集电极开路输出：  输出电路采用NPN晶体管，发射极直接连接0V端，集电极作为输出端,输出端和+V端之间为开路(Open)状态，这种输出形态即为NPN集电极开路输出。
![img](https://pic1.zhimg.com/80/v2-7cc1a732fa5156f644f63f63a890f7a7_1440w.png?source=d16d100b "NPN集电极开路输出")
集电极开路输出是以输出电路的晶体管发射极作为公共端，并且集电极悬空的输出电路。一般分为**NPN集电极开路输出**和**PNP集电极开路输出**。
![img](https://pic2.zhimg.com/80/v2-474826b279b723db94bd3bc7002f1604_1440w.png?source=d16d100b "PNP集电极开路输出")
由于输入信号为高电平时输出断开，需要使用外部“上拉”电阻，才能确保集电极电压达到所需的电平，即逻辑 1。因此，工程师在连接不同电压的系统时就更具灵活性：通过上拉电阻可将集电极电压上拉至不同电压，使之高于或低于编码器工作电压。
![img](https://pic1.zhimg.com/80/v2-d6295af6ac378d7132ce9743303aab23_1440w.png?source=d16d100b "集电极输出可上拉至适当电压以连接至外部系统")
不过，这种接口也具有一些缺陷。许多现成的控制器都已内置了上拉电阻，而这些上拉电阻会消耗电流，即产生耗散功率。此外，当该电阻与寄生电容组成 RC 电路时，输出在高电压与低电压之间的转换速率将因此降低。转换斜率即转换速率。
![img](https://pic2.zhimg.com/80/v2-d4b0bebb162543509b7bbb2d4bde8bf3_1440w.png?source=d16d100b "当输出在两种逻辑状态之间转换时，上拉电阻会显著降低输出电压转换速率")
通过降低转换速率，上拉电阻会显著降低编码器运行速度，从而降低增量编码器的分辨率。减小电阻值可以提高转换速率，但是当信号为低电平时，上拉电阻功耗的电流更大，耗散功率也更大。

当旋转编码器的电源电压与控制部的电源电压不一致时，此种输出方式为最佳使用方案[6]。
### 电压输出
电压输出型是在NPN型三极管的集电极与电源之间连接一个上拉电阻，这样集电极的输出电压会被钳制在一个稳定的范围。如下面这张图是欧姆龙E6B2-CWZ3E的电压输出电路：
![img](https://pic1.zhimg.com/80/v2-7eb289eb3a70135fcfa23e50cf962554_1440w.png?source=d16d100b "电压输出")


以输出电路的晶体管发射极为共通型，在集电极与电源间插入电阻，并输出因电压而变化的集电极的输出电路[7]。得集电极和电源之间能有一个稳定的电压状态。
电压输出就是集电极开路输出的反相增加一个电阻，构成一个极性是PNP或NPN，而另一个极性是电压，实际上就是NPN+电压或PNP+电压，这是针对是PNP的或NPN的形式的接收设备的一种权宜，便于两者都可以连接，但现在这种电压接口往往已经做在了经济型PLC上了，如果是那样的PLC，还是应该直接选集电极开路输出的，或电压型的极性相当的编码器，因为如果选电压输出型的编码器PNP+电压的，而连接的PLC是NPN+电压的，就会有漏电流而产生错误[8]。
![img](https://pic1.zhimg.com/80/v2-5b90efe118a21bc9767a4a5933dbe8b9_1440w.png?source=d16d100b "电压输出")
### 推挽输出
推挽输出（Push-Pull Output）电路由两个三极管组成，比如下面这张图的T1和T2。
![img](https://pica.zhimg.com/80/v2-6d166a736677c3697af2a0aacf380d3c_1440w.png?source=d16d100b "推挽输出（Push-Pull Output）电路")
推挽输出的两个三极管分别接受输入信号和该信号的反相信号，当输入信号（Input Signal）为1时，T1导通，此时输入信号的反相为0，因此T2截止；同样的，当输入信号为0时，T1截止，此时输入信号的反相为1，因此T2导通；可见推挽式输出电路可以输出信号的正反两相（比如A和A补），其抗干扰能力比较强，适合较远距离的传输。

推挽式放大输出，有的欧洲编码器用HTL表示，其相当于NPN+PNP的推挽放大，而且大部分有标准的集成放大电路，根据供电，输出10—30V，对于接收设备的兼容性强，信号强而稳定，若果再有与差分长线驱动一样有反相信号的话，因信号电压高，传递最远，差分传递及接受，抗干扰最好，工程项目或大型设备中，首选推挽式输出。
推挽式含反相6通道输出，也就是HTL-6。重型机械设备常常有较远传递或大变频电机工况下，就需要选推挽式并且含具有反相输出的推挽式输出编码器（例如ABB变频控制器，就有这样的接口。

推挽输出使用两个晶体管，而不是一个，因此可以弥补上述开集输出接口的缺陷。上部晶体管取代上拉电阻，导通时可将电压上拉至电源电压，由于电阻极小，因而转换速率较快。而输出信号为低电平时，晶体管关断，因此相较于开集电路，该有源上拉电路的耗散功率也相对较小，从而使电池供电设备的运行时间相对较长。
![img](https://pic4.zhimg.com/80/v2-23a64977fd11ebd5eff7cc14f64cd77f_1440w.png?source=d16d100b "推挽输出")
在+V和0V之间由两个晶体管回路构成的输出方式即为推拉输出方式。当输出信号为H时,上面的晶体管为ON,下面的晶体管为OFF。当输出信号为L时,则上面的晶体管为OFF,下面的晶体管为ON。推拉输出方式由于输出电流为两个方向(流入，流出)控制,输出阻抗较低,波形不容易失真,也不易受到干扰的影响。
当编码器的连接线较长时，可使用此输出方式。
### 差分线路驱动器输出（线路驱动器输出）
线驱动输出（Line Driver Output）电路是使用专用输出芯片，输出符合RS422标准的差分信号，抗干扰能力更强，适合用于传输速度较高、距离较远的场合。下面这张图，是欧姆龙E6B2-CWZ1X的线驱动输出电路：
![img](https://pic4.zhimg.com/80/v2-ff4f8d5809d87ddaaa1c4edbc4e4bd5b_1440w.png?source=d16d100b "线驱动输出（Line Driver Output）电路")
线性驱动输出是采用RS-422标准，用AM26LS31芯片应用于高速、长距离数据传输的输出模式。信号以差分形式输出，因此抗干扰能力更强。输出信号需专门能接收线性驱动输出的设备才能接收。输出电路见下图。
![img](https://pic1.zhimg.com/80/v2-0097393d85f865db9cac7b4cf55bf11c_1440w.png?source=d16d100b "线性驱动输出")
虽然使用推挽输出的编码器弥补了开集输出的一些缺陷，但两者都是单端输出。在布线距离较长的应用或存在电噪声和干扰的环境中，使用单端输出具有一定局限性。
布线距离较长时，信号幅度衰减，电容效应将减慢转换速率。由于单端信号的传输信号以地为参考，这类衰减就可能产生误差，从而导致系统性能下降。
此外，在电噪声环境中，不同幅度的干扰电压都将耦合到电缆上，从而导致单端系统的接收器错误地解码信号电压。
电缆长度超过一米时，建议使用差分信号。使用差分线路驱动器的编码器可产生两个输出信号：一个与原始信号相匹配，另一个与之完全相反，即互补信号。这两个信号之间的幅度差是原始单端信号的两倍，有助于克服电压降和电容引起的衰减问题。
![img](https://pic1.zhimg.com/80/v2-af244f8b62b0cc50825c308ff8059ccb_1440w.png?source=d16d100b "差分线路驱动器克服了信号衰减问题")
此外，由于两个信号均存在共模噪声，可以相互抵消，因此接收系统可忽略其影响。由于噪声抑制能力相当出色，差分线路驱动器接口广泛用于工业和汽车应用。多种 CUI 编码器都提供差分线路驱动器输出选项，可用于要求严苛的应用。
![img](https://pic1.zhimg.com/80/v2-92c0b00526b8637acfb6c6281ab274f7_1440w.png?source=d16d100b "差分接收器可忽略两个信号上同时存在的噪声")
本输出方式采用高速、长距离输送用的专用IC方式，该IC具有高速应答特性,适用于长距离传送，不易受干扰影响，是依据RS422-A规格的数据传送方式。信号以差动的2信号输出，因此抗干扰能力强。接受线路驱动器输出的信号时，可使用称为线路接
该IC具有高速应答特性,适用于长距离传送,不易受干扰影响。
### 补码输出（互补输出）
补码输出是输出上具备NPN和PNP 2种输出晶体管的输出电路。根据输出信号的「H」、「L」，2个输出晶体管交互进行「ON」、「OFF」动作。使用时，请在正极电源、OV上进行上拉、下降后再使用。补码输出，包括输出电流的流出、流入两个动作，其特征为信号的上、下降速度快，可延长代码的长距离。比集电极开路输出的电路传输距离能稍远，可与开路集电极输入机器（NPN、PNP）连接。
![img](https://pic4.zhimg.com/80/v2-ba3a294dc7607f322ddfec59f610ebc4_1440w.png?source=d16d100b "补码输出")
## 厂商

- [日本 NEMICON（内密控）](http://www.nemicon.org.cn/SeriesList.aspx?page=2&TypeId=20)
- [韩国 Autonics](https://www.autonicschina.cn/product/category/2000013)
- [欧姆龙（OMRON）自动化](https://www.fa.omron.com.cn/products/family/1607/)
- [宜科（ElCO）电子](http://www.elco-holding.com.cn/autoProduction?typeStr1=%E7%BC%96%E7%A0%81%E5%99%A8&typeStr2=%E5%85%89%E7%94%B5%E5%A2%9E%E9%87%8F%E7%BC%96%E7%A0%81%E5%99%A8)
- [西克(SICK)传感器](https://www.sick.com/cn/zh/motor-feedback-systems/c/g204351?q=:Def_Type:ProductFamily)
- [无锡瑞普海德](http://www.wxrep.com/)
- [长春禹衡光学](http://www.yu-heng.cn/product/22/)
- [光洋电子（无锡）](https://www.koyoele.com.cn/index.php/product/3)
- [上海佳武自动化](http://www.eltra-china.com/)

除了以上几点外，价格也是十分关键的因素，购置到性价比高的产品当然是每个使用者最理想的想法，在选择旋转编码器时要选择价格与设备性能相符并且符合自己使用的产品，对于同类产品进行多方面的对比，在选择编码器厂家时也要尽量选择大型公司，产品质量和服务质量更加有保障。
# 硬件电路及接线
## 电路
注意上拉电阻及信号线的104电容滤波，可以使信号更稳定[10]。
![img](https://pic1.zhimg.com/80/v2-122eae2cdb6f62cfe6c54da55fb083d8_1440w.png?source=d16d100b)
## 接线（PLC）
增量编码器的连接，要清楚编码器的信号输出形式与接收设备的匹配问题，选编码器或选接收设备一定要两者信号形式的匹配。增量编码器的信号输出从波形上看，分**正余弦输出**（sin/cos）与**方波输出**两种。
①.  正余弦输出的信号是模拟量变化的信号周期，又分电压输出1Vpp和电流输出1uApp，这两种输出一般PLC都没有接口，大部分是连接专用的运动控制卡，其内部可做细分而获得更高的分辨率和动态特性，也有连接专用的细分盒再细分后输出方波的，选型时搞清楚是电压输出还是电流输出（现在大部分是电压输出了）。   
②. 方波输出的也有分集电极开路输出（NPN或PNP）、电压输出、差分长线驱动、推挽式输出等。


1、开路集电极输出[9]

集电极开路输出就是类晶体管放大电路，三极管放大集电极开路输出，依据三极管的极性，分NPN与PNP，接收设备选型需要匹配不可选错，这种输出电路简单经济，但选型面窄，传输距离根据放大管有远有近，但总体传递距离不远，且保护不够，较易损坏，大部分用在单机设备上而不是工程项目中。这种输出的电压依据供电，有5-12V输出和12—24V输出，这也要搞清楚才能确保信号的连接。

   - NPN型集电极输出

![img](https://pic1.zhimg.com/80/v2-86aa966c69563622c622bee26bd1e069_1440w.png?source=d16d100b)

   - PNP型集电极输出

![img](https://pic3.zhimg.com/80/v2-e9e4e0a877199e02cb6a5be600ea1621_1440w.png?source=d16d100b)

2、 线性驱动输出（差分信号）

差分长线驱动是一种差分放大的电路，大部分是5V，提供A+、B+、Z+及其180度反相的A—、B—、Z—，读取时，以A+与A-差分值读取，对于共摸干扰有抑制作用，传递距离较远，由于抗干扰能力较强，在运动控制(数控机床）中用得较多。
![img](https://pic4.zhimg.com/80/v2-959cd927679803380a1146f145290533_1440w.png?source=d16d100b)

3、 电压输出型

电压输出就是集电极开路输出的反相增加一个电阻，构成一个极性是PNP或NPN，而另一个极性是电压，实际上就是NPN+电压或PNP+电压，这是针对是PNP的或NPN的形式的接收设备的一种权宜，便于两者都可以连接，但现在这种电压接口往往已经做在了经济型PLC上了，如果是那样的PLC，还是应该直接选集电极开路输出的，或电压型的极性相当的编码器，因为如果选电压输出型的编码器PNP+电压的，而连接的PLC是NPN+电压的，就会有漏电流而产生错误。
![img](https://pica.zhimg.com/80/v2-ebcc38294b443611afff3553f6122470_1440w.png?source=d16d100b)

4、 互补输出型

![img](https://pic1.zhimg.com/80/v2-bd971e67774fb26f984ab2e5ca594018_1440w.png?source=d16d100b)

# 编码
## Raspberry Pi Pico

- C

```c
#include <stdio.h>
#include "pico/stdlib.h"
#define PinA 27
#define PinB 26
#define PinSW 22
uint64_t time = 0;
int32_t count = 0;
int32_t num = 0;

void blinkA()
{
    if ((time_us_64() - time) > 3000)
    {
        if (gpio_get(PinB))
        {
            count++;
        }
        else
        {
            count--;
        }
    }

    time = time_us_64();
}

void init()
{
    gpio_init(PinA);
    gpio_init(PinB);
    gpio_init(PinSW);
    gpio_set_irq_enabled_with_callback(PinA, GPIO_IRQ_LEVEL_LOW, true, &blinkA);
    gpio_set_dir(PinA, 0);
    gpio_set_dir(PinB, 0);
    gpio_set_dir(PinSW, 0);
}
int main()
{
    stdio_init_all();
    init();
    time = time_us_64();
    int i=0;
    while (1)
    {
        if (num != count)
        {
            i = 1;
            num = count;
            printf("%ld\r\n", num);
        }
        if((!gpio_get(PinSW)) && 1 == i)
        {
            i = 0;          
            count = 0;        
            while(!gpio_get(PinSW));
        }
    }

    return 0;
}

```

- Python

```python
from machine import Pin,ADC
import utime

#Define pins and their initialization
right = machine.Pin(27, machine.Pin.IN)
left = machine.Pin(26, machine.Pin.IN)
down = machine.Pin(22, machine.Pin.IN)


right_assist = 0
count = 0             #Rotate the value of the encoder


def right_handler(pin):
    global right_assist
    right.irq(handler=None)
    right_assist = 1


def down_handler(pin):
    global count
    down.irq(handler=None)
    count = 0
    print("down",count)
    down.irq(trigger=machine.Pin.IRQ_FALLING, handler=down_handler)


right.irq(trigger=machine.Pin.IRQ_FALLING, handler=right_handler)
down.irq(trigger=machine.Pin.IRQ_FALLING, handler=down_handler)


while True :
    if (right_assist == 1 ):
        
        if (left.value() == 1 ):
            count = count - 1
            print("left",  count)

        elif (left.value() == 0 ):
            count = count + 1
            print("right",  count)
            
        while (left.value() == 0 ) | (right.value() == 0):
            utime.sleep_ms(1)
            
        right_assist = 0
        right.irq(trigger=machine.Pin.IRQ_FALLING, handler=right_handler)
```

## STM32

```c
/* USER CODE BEGIN Header */
/**
******************************************************************************
* @file           : main.c
* @brief          : Main program body
******************************************************************************
* @attention
*
* <h2><center>&copy; Copyright (c) 2021 STMicroelectronics.
* All rights reserved.</center></h2>
*
* This software component is licensed by ST under BSD 3-Clause license,
* the "License"; You may not use this file except in compliance with the
* License. You may obtain a copy of the License at:
*                        opensource.org/licenses/BSD-3-Clause
*
******************************************************************************
*/
/* USER CODE END Header */
/* Includes ------------------------------------------------------------------*/
#include "main.h"
#include "usart.h"
#include "gpio.h"

/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */

/* USER CODE END Includes */

/* Private typedef -----------------------------------------------------------*/
/* USER CODE BEGIN PTD */
uint8_t turn_flag;
int32_t count;
/* USER CODE END PTD */

/* Private define ------------------------------------------------------------*/
/* USER CODE BEGIN PD */
/* USER CODE END PD */

/* Private macro -------------------------------------------------------------*/
/* USER CODE BEGIN PM */

/* USER CODE END PM */

/* Private variables ---------------------------------------------------------*/

/* USER CODE BEGIN PV */

/* USER CODE END PV */

/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
/* USER CODE BEGIN PFP */

/* USER CODE END PFP */

/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */
#include "stdio.h"

#ifdef __GNUC__
#define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
#define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif
PUTCHAR_PROTOTYPE
{
HAL_UART_Transmit(&huart2 , (uint8_t *)&ch, 1, 0xFFFF);
return ch;
}
/* USER CODE END 0 */

/**
* @brief  The application entry point.
* @retval int
*/
int main(void)
{
    /* USER CODE BEGIN 1 */
    
    /* USER CODE END 1 */
    
    /* MCU Configuration--------------------------------------------------------*/
    
    /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
    HAL_Init();
    
    /* USER CODE BEGIN Init */
    
    /* USER CODE END Init */
    
    /* Configure the system clock */
    SystemClock_Config();
    
    /* USER CODE BEGIN SysInit */
    
    /* USER CODE END SysInit */
    
    /* Initialize all configured peripherals */
    MX_GPIO_Init();
    MX_USART2_UART_Init();
    /* USER CODE BEGIN 2 */
    
    /* USER CODE END 2 */
    
    /* Infinite loop */
    /* USER CODE BEGIN WHILE */
    while (1)
    {	
        if(turn_flag == 1)
        {
            if(HAL_GPIO_ReadPin(SIB_GPIO_Port, SIB_Pin ) == GPIO_PIN_RESET )
            {
                count --;
                printf("Turn left!\r\n");
                while(HAL_GPIO_ReadPin(SIA_GPIO_Port, SIA_Pin ) == GPIO_PIN_RESET);
                
            }			
            else 
            {
                count ++;
                printf("Turn right!\r\n");
                while(HAL_GPIO_ReadPin(SIA_GPIO_Port, SIA_Pin ) == GPIO_PIN_SET);
            }
            
            
            printf("count = %d \r\n",count);
            turn_flag = 0;
        }
        
        if(HAL_GPIO_ReadPin(SW_GPIO_Port, SW_Pin ) == GPIO_PIN_RESET )
        {
            HAL_Delay(10);
            if(HAL_GPIO_ReadPin(SW_GPIO_Port, SW_Pin ) == GPIO_PIN_RESET )
            {
                turn_flag = 0;
                count = 0;
                printf("Turn down!\r\n");
                printf("count = %d \r\n",count);
                while(HAL_GPIO_ReadPin(SW_GPIO_Port, SW_Pin ) == GPIO_PIN_RESET);
            }
        }
        
        /* USER CODE END WHILE */
        
        /* USER CODE BEGIN 3 */
    }
    /* USER CODE END 3 */
}

/**
* @brief System Clock Configuration
* @retval None
*/
void SystemClock_Config(void)
{
    RCC_OscInitTypeDef RCC_OscInitStruct = {0};
    RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};
    
    /** Initializes the RCC Oscillators according to the specified parameters
    * in the RCC_OscInitTypeDef structure.
    */
    RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSE;
    RCC_OscInitStruct.HSEState = RCC_HSE_ON;
    RCC_OscInitStruct.HSEPredivValue = RCC_HSE_PREDIV_DIV1;
    RCC_OscInitStruct.HSIState = RCC_HSI_ON;
    RCC_OscInitStruct.PLL.PLLState = RCC_PLL_ON;
    RCC_OscInitStruct.PLL.PLLSource = RCC_PLLSOURCE_HSE;
    RCC_OscInitStruct.PLL.PLLMUL = RCC_PLL_MUL9;
    if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
    {
        Error_Handler();
    }
    /** Initializes the CPU, AHB and APB buses clocks
    */
    RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK|RCC_CLOCKTYPE_SYSCLK
        |RCC_CLOCKTYPE_PCLK1|RCC_CLOCKTYPE_PCLK2;
    RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_PLLCLK;
    RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
    RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV2;
    RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;
    
    if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_2) != HAL_OK)
    {
        Error_Handler();
    }
}

/* USER CODE BEGIN 4 */

/**
* @brief EXTI line detection callbacks
* @param GPIO_Pin: Specifies the pins connected EXTI line
* @retval None
*/
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
    if((GPIO_Pin == SIA_Pin) && (turn_flag == 0))
    {
        turn_flag = 1;
    }
}

/* USER CODE END 4 */

/**
* @brief  This function is executed in case of error occurrence.
* @retval None
*/
void Error_Handler(void)
{
    /* USER CODE BEGIN Error_Handler_Debug */
    /* User can add his own implementation to report the HAL error return state */
    __disable_irq();
    while (1)
    {
    }
    /* USER CODE END Error_Handler_Debug */
}

#ifdef  USE_FULL_ASSERT
/**
* @brief  Reports the name of the source file and the source line number
*         where the assert_param error has occurred.
* @param  file: pointer to the source file name
* @param  line: assert_param error line source number
* @retval None
*/
void assert_failed(uint8_t *file, uint32_t line)
{
    /* USER CODE BEGIN 6 */
    /* User can add his own implementation to report the file name and line number,
    ex: printf("Wrong parameters value: file %s on line %d\r\n", file, line) */
    /* USER CODE END 6 */
}
#endif /* USE_FULL_ASSERT */

/************************ (C) COPYRIGHT STMicroelectronics *****END OF FILE****/

```

## Arduino

```c
int cnt = 0;

void setup() {
  // put your setup code here, to run once:
  Serial.begin(115200);
  pinMode(3, OUTPUT);
  pinMode(4, OUTPUT);
  pinMode(5, OUTPUT);
}

void loop() {
  // put your main code here, to run repeatedly:

  Serial.println(cnt);
  Serial.println("Hello World\r\n");
//  digitalWrite(3, HIGH); 
//  delay(1000);
//  digitalWrite(3, LOW); 
//  delay(1000);  
//  digitalWrite(4, HIGH); 
//  delay(1000);
//  digitalWrite(4, LOW); 
//  delay(1000);
  digitalWrite(5, HIGH); 
  delay(1000);
  digitalWrite(5, LOW); 
  delay(1000);
  cnt++;   
}
```

# 参考

1. [Omron：旋转式编码器](https://www.fa.omron.com.cn/guide/cgq/1718.html)
1. [电子森林：基于STEP FPGA的旋转编码器电路驱动](https://www.eetree.cn/wiki/%E6%97%8B%E8%BD%AC%E7%BC%96%E7%A0%81%E5%99%A8%E6%A8%A1%E5%9D%97#)
1. [Wiki：旋转编码器](https://zh.wikipedia.org/zh-hans/%E6%97%8B%E8%BD%89%E7%B7%A8%E7%A2%BC%E5%99%A8)
1. [Omron:增量型编码器输出A相、B相和Z相分别代表什么含义？](https://www.fa.omron.com.cn/txtfaq/3760.html)
1. [Digi-Key:了解编码器输出信号有助于选择最佳设备](https://www.digikey.cn/zh/articles/understanding-encoder-output-signals-to-aid-optimum-device-selection)
1. [Autonics:编码器NPN集电极开路输出、推拉输出、线性驱动输出的特点](http://www.autonics-china.com.cn/index/news/info/id/PTWIk.html)
1. [Oromn：编码器的电压输出，集电极信号输出，线性驱动输出的区别？](https://www.fa.omron.com.cn/txtfaq/7536.html)
1. [工控网：增量编码器的常用信号输出有哪些？](http://www.gongkong.com/article/202009/90788.html)
1. [编码器的集电极输出、电压输出、互补输出和线性驱动输出](https://www.cnblogs.com/jndx-ShawnXie/p/10012525.html)
1. [微雪：Rotation Sensor](https://www.waveshare.net/wiki/Rotation_Sensor)
1. [知乎：谈谈增量型编码器的信号电路输出方式](https://zhuanlan.zhihu.com/p/31163840)
