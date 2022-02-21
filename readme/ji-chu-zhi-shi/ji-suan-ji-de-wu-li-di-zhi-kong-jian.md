# 计算机的物理地址空间



机器刚刚启动的时候，物理内存是按照下面这样划分的：

![](../../.gitbook/assets/image-20220215105947152.png)

第一代`PC`基于16位`Intel 8088`处理器，只能寻址1MB的物理内存。所以早期PC的物理地址空间将从`0x00000000`开始，到`0x000FFFFF`结束，而不是`0xFFFFFFFF`（32位）。标记为`Low Memory`的`640KB`空间是早期PC能够使用的唯一随机访问内存(`RAM`)。

从`0x000A0000`到`0x000FFFFF`的`384KB`区域（也就是`640KB`到`1MB`之间的区域）由**硬件预留**，用于特殊用途，如视频显示缓冲区（显存）和一些系统固件。这个保留区域中最重要的部分是\*\*`Basic Input/Output System (BIOS)`\*\*（下面会介绍这个概念），它占用了从`0x000F0000`到`0x000FFFFF`的`64KB`区域。

在早期的`PC`中，`BIOS`保存在真正的只读存储器（`ROM`）中，但现在的`pc`将BIOS存储在可更新的闪存中。**`BIOS`主要负责对系统进行基本的初始化操作**，如激活显卡、检查内存安装量等。执行这个初始化之后，BIOS**从一些适当的位置（比如硬盘）加载操作系统，并将机器的控制权传递给操作系统**。（划重点，BIOS的作用。）

随着技术的发展，`Intel`最终使用`80286`和`80386`处理器突破了`1MB`的寻址，它们分别支持`16MB`和`4GB`的物理地址空间，但`PC`架构师仍然保留了原始的`1MB`物理地址空间布局，以确保与现有软件的**向后兼容性**。因此，现代`pc`在物理内存中有一个从`0x000A0000`到`0x00100000`的一个洞，将`RAM`划分为 `“low memory”` 或 `“conventional memory”` （前`640KB`）和 `“extended memory”` （其他的部分）。

最近的`x86`处理器可以支持超过`4GB`的物理`RAM`，所以`RAM`可以扩展到`0xFFFFFFFF`以上。在这种情况下，`BIOS`必须安排在系统`RAM`的32位可寻址区域的顶部留下第二个洞，为这些32位设备的映射留下空间。（实际上是虚拟内存）
