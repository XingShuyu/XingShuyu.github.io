---
title: CTF Flare-on 2015 Challenge4
date: 2025-12-17 17:54:56
tags: CTF
---
# 动态脱壳
## UPX加密找OEP
UPX加密，打开后会是一段自解压的SMC(self modifying code)。  
然后我们找关键段:```pusha```和```popa```。  
这两个指令分别是把寄存器的值全部压入栈和从栈中弹出到寄存器中。  
我们在```pusha```后面设置断点，然后单步执行到```popa```前面，这样就能拿到解密后的代码段。  
> 如果你用的IDA进入了无限循环，记得注意一下这个地方  
> ~~原本是jnz,这里我改掉了~~
> ![loop](/images/CTF/Flare-on_2015_Challenge4/image.png)  
> patch一下跳过这个循环就好了  
底下这个jmp的地址其实就算OEP(Original Entry Point)，我们可以直接跳过去  
![alt text](/images/CTF/Flare-on_2015_Challenge4/image-1.png)  
## 在OEP中找main函数
main函数有一个特别明显的特点。  
有三个参数，argc, argv, envp。  
我们可以通过观察栈来找到main函数的入口。  
还有步过的时候会运行（）  
![alt text](/images/CTF/Flare-on_2015_Challenge4/image-3.png)
# 分析main函数
~~写到这里的时候楼主已经做完，一想到接下来要写什么我就觉得好笑~~  
我们打开main函数，发现有一个地方会检查argc数量并且获取第一个参数argv  
![alt text](/images/CTF/Flare-on_2015_Challenge4/image-2.png)
交叉引用跟踪一下，发现对输入进行了md5处理。  
底下有一个简单的查验输入，然而，输入只是查验了一下，不参与最后的flag生成!  
![alt text](/images/CTF/Flare-on_2015_Challenge4/image-4.png)
那还说啥呢，修改EIP强制通过就完事了。  
往下看，发现程序结尾有一个cout  
![alt text](/images/CTF/Flare-on_2015_Challenge4/image-5.png)
跑过去的时候程序会输出flag
```Text
Uhr1thm3tic@flare-on.com
```