---
layout: post
title: C语言中结构体struct与联合体union
tags:
  - CLanguage
hero: https://source.unsplash.com/random
overlay: blue
---
struct（结构体）与union（联合体）是C语言中两种不同的数据结构，两者都是常见的**复合数据类型**，其区别主要体现在两个方面。
{: .lead}

# 定义
<!–-break-–> 

## 结构体（struct）

在C语言中，结构体（struct）指的是一种数据结构，是C语言中复合数据类型（aggregate data type）的一类。结构体可以被声明为变量、指针或数组等，用以实现较复杂的数据结构。结构体同时也是一些元素的集合，这些元素称为结构体的成员（member），且这些成员可以为不同的类型，成员一般用名字访问。

结构体的定义如下所示，**struct**为结构体**关键字**，**tag**为结构体的**标志**，**member-list**为结构体**成员列表**，其必须列出其所有成员；**variable-list**为此结构体**声明的变量**。
```c
    struct tag
    {
        member-list 
    }variable-list; 
```

在一般情况下，tag、member-list、variable-list这3部分至少要出现2个。以下为示例：
```c
    //此声明声明了拥有3个成员的结构体，分别为整型的a，字符型的b和双精度的c
    //同时又声明了结构体变量s1
    //这个结构体并没有标明其标签
    struct 
    {
        int a;
        char b;
        double c;
    } s1;
    
    //此声明声明了拥有3个成员的结构体，分别为整型的a，字符型的b和双精度的c
    //结构体的标签被命名为SIMPLE,没有声明变量
    struct SIMPLE
    {
        int a;
        char b;
        double c;
    };
    //用SIMPLE标签的结构体，另外声明了变量t1、t2、t3
    struct SIMPLE t1, t2[20], *t3;

    //也可以用typedef创建新类型
    typedef struct
    {
        int a;
        char b;
        double c; 
    } Simple2;
    //现在可以用Simple2作为类型声明新的结构体变量
    Simple2 u1, u2[20], *u3;
```
结构体成员依据结构体变量类型的不同，一般有2种访问方式，一种为直接访问，一种为间接访问。

* 直接访问应用于普通的结构体变量。直接访问使用 **结构体变量名.成员名**，

* 间接访问应用于指向结构体变量的指针。间接访问使用 **(\*结构体指针名).成员名** 或者使用 **结构体指针名->成员名**。相同的成员名称依靠不同的变量前缀区分。
```c
  struct SIMPLE
  {
      int a;
      char b;
  };

  //声明结构体变量s1和指向结构体变量的指针s2
  struct SIMPLE s1, *s2;

  //给变量s1和s2的成员赋值,注意s1.a和s2->a并不是同一成员
  s1.a = 5;
  s1.b = 6;
  s2->a = 3;
  s2->b = 4;
```
## 联合体（union）

在[计算机科学](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%25E8%25AE%25A1%25E7%25AE%2597%25E6%259C%25BA%25E7%25A7%2591%25E5%25AD%25A6)中，**联合体**（英语：**union**）又名**共用体**，是一种具有多个类型或格式的值，或者把它定义为一种由具有这样的值的[变量](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/w/index.php%3Ftitle%3D%25E5%258F%2598%25E9%2587%258F_%28%25E7%25A8%258B%25E5%25BA%258F%25E8%25AE%25BE%25E8%25AE%25A1%29%25E5%258F%2598%25E9%2587%258F%26action%3Dedit%26redlink%3D1)形成的[数据结构](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%25E6%2595%25B0%25E6%258D%25AE%25E7%25BB%2593%25E6%259E%2584)。一些[编程语言](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%25E7%25BC%2596%25E7%25A8%258B%25E8%25AF%25AD%25E8%25A8%2580)可以支持被称为“联合体”的特殊的[资料类型](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%25E8%25B3%2587%25E6%2596%2599%25E9%25A1%259E%25E5%259E%258B)，来表示上述的变量。与[枚举](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%25E6%259E%259A%25E4%25B8%25BE)和[结构体](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%25E7%25BB%2593%25E6%259E%2584%25E4%25BD%2593)不同的是，一个联合体的长度等于其内部长度最大的成员的长度，并且它们都共享着同一段内存。

在[C语言](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/C%25E8%25AA%259E%25E8%25A8%2580)中，一个典型的例子如下：
```c
    union name1
    {
       int     a;
       float   b;
       char    c;
    } uvar;
```
# 区别

结构体
```c
    struct MyStruct
    {
        double a;
        int b;
        char c;
    };
    struct MyStruct value;
```
联合体
```c
    union MyUnion
    {
        double a;
        int b;
        char c;
    };
    union MyUnion value;
```
![](https://pic2.zhimg.com/v2-172205ed968b22eb59cf126d71391331_b.jpg)

变量value内存空间占用情况

## **一、内存占用**

结构体（struct）和联合体（union）都是由不同的数据类型成员组成的，但是结构体所有成员占用的内存空间是累加的，而联合体（union）中所有的成员公用一块地址空间。结构体（struct）中的内存空间等于所有成员长度之和；联合体（union）成员不能同时占用内存空间，长度等于最长成员的长度。

* 结构体：空间包容性强，但是内存空间必须全部分配

* 联合体：成员是互斥的，但是可以大大节省内存空间

```c
  typedef union {double i; int k[5]; char c;}DATE;
  typedef struct data( int cat; DATE cow;double dog;）too;
  DATE max;
  printf ("%d", sizeof(too)+sizeof(max));
```

假设为32位机器，int型占4个字节， double型占8个字节，char型占1个字节，而DATE是一个联合型变量，联合型变量共用空间，uion里面最大的变量类型是int\[5\]，所以占用20个字节，它的大小是20，而由于 union中double占了8个字节，因此 union是要8个字节对齐，所占内存空间为8的倍数。为了实现8个字节对齐，所占空间为24.而data是一个结构体变量，每个变量分开占用空间，依次为 sizeof（int）+ sizeof（DATE）+ sizeof（ double）=4+24+8=36按照8字节对齐，占用空间为40，所以结果为40+24=64。

## **二、成员互斥**

在实际应用中我们一般都使用结构体来定义数据组合而成的结构型变量，而在各数据类型各变量占用空间差不多并且**对各变量同时使用要求不高**的场合（单从内存使用上)也可以灵活的使用union。

# 应用

## 1、变量的初始化

在初始化的时候，只应对一个成员进行初始化即在初始化列表中只有一个初始值。原因就是联合体的所有成员共用一个首地址，在默认情况下，会将这个初始值初始化给联合体变量的第一个成员。
```c
    union MyUnion
    {
        double a;
        int b;
        char c;
    };
    //为第一个成员初始化
    union MyUnion un1 = {5.0f};
    
    //错误初始化，不能为多个成员初始化
    union MyUnion un1 = {5.0f, 10};
    
    //对其它位置的成员进行初始化，则可以通过指定初始化方式
    union MyUnion un1 = {.b = 10};
    
    //与结构体一样，也可以将一个联合体变量作为初始值，直接初始化给同类型的另一个联合体变量
    union MyUnion un2 = un1;
```
## **2、数据位操作**
```c
    #include<stdio.h>
    typedef struct
    {
      unsigned char bit0:1;
      unsigned char bit1:1;
      unsigned char bit2:1;
      unsigned char bit3:1;
      unsigned char bit4:1;
      unsigned char bit5:1;
      unsigned char bit6:1;
      unsigned char bit7:1;
    }bitValue;
    
    typedef union
    {
      unsigned char bytedata;
      bitValue  bitdata;
    }regValue;
    
    int main()
    {
      regValue data;
    
      data.bytedata= 0x5A;
      printf("%d",data.bitdata.bit5);  //读取第6位
    
      data.bitdata.bit7 = 1;           //修改第8位
      return 0;
    }
```
可以看出，通过访问和修改联合体中的定义bitdata成员，可以间接的访问和修改定义的bytedata的值，这可以用在嵌入式的寄存器位操作上。

## **3、和struct嵌套使用**

比如我们分别定义电视和空调的属性：
```c
    struct tvFeature    //电视属性
    {
       char *logo;     //品牌
       int price;      //价格
       int screensize  //屏幕尺寸
       int resolution  //分辨率
    }tvFeature;
    struct tvFeature tvfeature;
    
    struct airFeature  //空调属性
    {
       char *logo; //品牌
       int price;   //价格
       int coldcapacity;//制冷量
       int hotcapacity;//制热量
    }airFeature;
    struct airFeature airfeature;
```
可以看出电视和空调有相同的属性，也有各自特有的属性。我们可以使用家用电器的数据结构统一定义。但是这样用统一的数据结构，定义电视和空调的变量之间耦合会增加很多，对于tvfeature和airfeature各自来说用不到的属性也会浪费内存空间。
```c
    struct homeappliancesFeature  //电器属性
    {
       char *logo; //品牌
       int price;   //价格
       int screensize  //屏幕尺寸
       int resolution  //分辨率
       int coldcapacity;//制冷量
       int hotcapacity;//制热量
    }homeappliancesFeature;
    
    struct homeappliancesFeature tvfeature;
    struct homeappliancesFeature airfeature;
```
因此**可以用union来解决**问题：
```c
    struct tvFeature    //电视属性
    {
       int screensize  //屏幕尺寸
       int resolution  //分辨率
    }tvFeature;
    
    struct airFeature  //空调属性
    {
       int coldcapacity;//制冷量
       int hotcapacity;//制热量
    }airFeature;
    
    struct homeappliancesFeature  //电器属性
    {
       char *logo; //品牌
       long country; //国家
       union
       {
          struct tvFeature tvST;
          struct airFeature airST;
       };
    };
    
    struct homeappliancesFeature tvfeature;
    struct homeappliancesFeature airfeature;
```
如上我们只需一个结构体，就可以解决电视和空调的属性不同问题；struct tvFeature tvST和struct airFeature airST共用一块内存空间，定义变量时，可以访问各自的特有属性，这样就解决了内存浪费和变量耦合高的问题。

## **4、数据复制**

例如串口数据发送时，可以直接使用数据复制的方式将数据打包发送，不需要将一个4字节的数据额外进行拆分为4个单字节的数据；反之读取数据时，也可以不用将4个单字节的数据重新通过移位拼接为一个4字节数据。
```c
    typedef union
    {
      uint8   data8[4];
      uint32  data32;
    }dataType;
    
    uint32 sendData = 0x5A5AA5A5;
    uint32 receiveData;
    dataType commSend;
    
    void main(void)
    {
        uint8 commData[128];
        //数据复制
        commData.data32 = sendData;
        //发送数据,字节复制,不需要再将commData.data32单独移位拆分
        commData[0]= commSend.data8[0];
        commData[1]= commSend.data8[1];
        commData[2]= commSend.data8[2];
        commData[3]= commSend.data8[3];
    
        //读取数据时,字节复制,不需要再将已经读取到的4个单字节数据拼接
        receiveData =  commData.data32;
    }
```
## 5、分时发送不同帧格式数据

比如需要在同一段通信数据发送逻辑中，针对不同通信协议帧格式进行发送时，就可以这样定义数据结构。
```c
    typedef struct
    {
       uint8 head;   //帧头格式相同
       union    //中间数据格式不一样
       {
          struct             //payloadType1
          {
            uint8 cmd;
            uint8 type;
            uint8 data[5];
            uint8 check;
          }msgType1;
    
          struct              //payloadType2
          {
            uint16 cmd;
            uint8 data[8];
            uint16 check;
          }msgType2;
    
         uint8 data[10];      //payloadType3
       } payloadType;
       uint8 end;    //帧尾格式相同
    }frameType;
```
注意


## 数据大小端

使用联合体时需要注意数据大小端问题，这个取决于实际的处理器的存储方式。

大端存储就是高字节数据放在低地址。

小端存储就是高字节数据放在高地址。

如下方例子，可以知道使用的处理器的存储方式：
```c
    #include<stdio.h>
    union Un
    {
      int i;
      char c;
    };
    union Un un;
    
    int main()
    {
      un.i = 0x11223344;
      if (un.c == 0x11)
      {
        printf("大端\n");
      }
      else if (un.c == 0x44)
      {
        printf("小端\n");
      }
    }
```
## **指针方式访问**

由于在一个成员长度不同的联合体里，分配给联合体的内存大小取决于它的最大成员的大小。如果内部成员的大小相差太大，当存储长度较短的成员时，浪费的空间是相当可观的，在这种情况下，更好的方法是在联合体中存储指向不同成员的指针而不是直接存储成员本身。所有指针的长度都是相同的，这样能解决内存空间浪费的问题。
```c
    #include<stdio.h>
    typedef struct
    {
      unsigned char a;
      int b;
    }stValue1;
    
    typedef struct
    {
      int c;
      unsigned char d[10];
      double e;
    }stValue2;
    
    //联合体成员定义为指针成员
    union Un
    {
      stValue1 *ptrSt1;
      stValue2 *ptrSt2;
    };
    
    int main()
    {
      union Un *info;
      info->ptrSt1->a = 5;
      info->ptrSt2->e = 9.7f;
    }
```

总之，在实际使用联合体union过程中一句话总结：围绕**成员互斥**和**内存共享**这两个核心点去灵活设计数据结构。

Reference
---------

1.  [在实际应用中联合体union的妙用](https://link.zhihu.com/?target=https%3A//www.cnblogs.com/Sharemaker/p/16962105.html)
2.  仲一：嵌入式软件开发笔试面试指南
3.  [wiki：联合体（union）](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%25E8%2581%2594%25E5%2590%2588%25E4%25BD%2593)
4.  [wiki：结构体（struct）](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%25E7%25BB%2593%25E6%259E%2584%25E4%25BD%2593_%28C%25E8%25AF%25AD%25E8%25A8%2580%29)
