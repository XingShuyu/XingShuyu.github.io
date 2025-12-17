---
title: 2025 Mini-L s1gn1n
date: 2025-12-17 19:50:21
tags: CTF
categories: CTF
---
## 静态流程分析
我们打开start入口点，找到主函数，发现这里主要进行判断的函数是sub_F914D0  
![判断核心](/images/CTF/2025_Mini-L_s1gn1n/1762577690-823570-image.png)
那么最后的返回值必须是0  
我们来看函数处理  
![函数处理](/images/CTF/2025_Mini-L_s1gn1n/1762577716-295074-image-1.png)
我们用findCrypt可以找到这里存在一个base64加密函数，  
看看加密的字符串，v9是上面处理出来的，我们再逆向往上看，sub_F91470  
Emm,,这个左中右嵌套，很像是二叉树遍历啊  
再看上一个函数，subF912E0对输入内容a1进行了一个树的创建，这里用了队列，应该是逐层输入二叉树  
~~这个二叉树逐层输入妙啊~~  
再看下面的倒序循环，加密结果每一位和上一位进行异或，再与byte_F94060进行异或，得到结果  
这里我们注意：当i == 0 时，循环已经退出，不会对第一位进行处理
最后的for循环里面每次减少了1，我们可以猜测，只有第一位是有数字的，其他的都变成了0  
那么第一位的数字是多少呢？  
我们看byte里面一共有60个，每个都需要异或的话，那大概率是循环60次了  
那么第一位数组就是28+60 = 88  
这下我们理清了检测逻辑：  
先对输入的字符进行二叉树遍历，对遍历结果进行base64加密，然后先与上一位异或，异或结果是byte_F94060,第一位不进行处理，是88。  
## 逆向推出输入
我们由上文的到，每一位在异或前是byte_F94060^上一位，我们先得出异或前内容（base64加密）  
```c++
    int enc[] = {0x58,0x69,0x7B,0x6,0x1E,0x38,0x2C,0x20,0x4,0x0F,0x1,0x7,0x31,0x6B,0x8,0x0E,0x7A,0x0A,0x72,0x72,0x26,0x37,0x6F,0x49,0x21,0x16,0x11,0x2F,0x1A,0x0D,0x3C,0x1F,0x2B,0x32,0x1A,0x34,0x37,0x7F,0x3,0x44,0x16,0x0E,0x1,0x28,0x1E,0x68,0x64,0x23,0x17,0x9,0x3D,0x64,0x6A,0x69,0x63,0x18,0x18,0x0A,0x15,0x70};
    int dec[60] = {88};
    for(int i = 1;i<60;i++){
        dec[i]=enc[i]^dec[i-1];
    }
    for(int i = 0; i < 60; i++)
    {
        cout<<(char)dec[i];
    }
    //  X1JLRjFfbmlkZ197MG5GaV9pQGVycnRMfTNzM21ucmlDZ2VubkV2X1RJRXM=
    // //dec[] = "_RKF1_nidg_{0nFi_i@errtL}3s3mnriCgennEv_TIEs"
```  
之后我们先不考虑直接二叉树逆向回去，我们已经知道输入的总字数是44，我们用原来的程序看一下，44个字符二叉树排序结果是什么：
```Text
abcdefghigklmnopqrstuvwxyzABCDEFGHIGKLMNOPQR
```
排序后：
```Text
FpGhHqIdGrKiLsMbNtOgPuQeRvkwaxlyfzmAcBnCgDoE
```
我们来建一个对应量S，
```C++
    char decoded[] = "abcdefghigklmnopqrstuvwxyzABCDEFGHIGKLMNOPQR";
    char encoded[] = "FpGhHqIdGrKiLsMbNtOgPuQeRvkwaxlyfzmAcBnCgDoE";
    int S[44];
    for(int i=0;i<44;i++){
        for(int j = 0;j<44;j++){
            if(encoded[j] == decoded[i]){
                S[i] = j;
            }
        }
    }
    for(int i = 0; i < 44;i++){
        cout<<S[i]<<",";
    }
```
最后对应量
```Text
S[] = {28,15,36,7,23,32,40,3,11,40,26,30,34,38,42,1,5,9,13,17,21,25,27,29,31,33,35,37,39,41,43,0,8,4,6,8,10,12,14,16,18,20,22,24};
```

处理一下：
```C++
    int S[] = {28,15,36,7,23,32,40,3,11,40,26,30,34,38,42,1,5,9,13,17,21,25,27,29,31,33,35,37,39,41,43,0,8,4,6,8,10,12,14,16,18,20,22,24};
    char dec[] = "_RKF1_nidg_{0nFi_i@errtL}3s3mnriCgennEv_TIEs";
    char enc[44];
    for (int i = 0; i < 44;i++){
        enc[i] = dec[S[i]];
    }
    for (int i = 0;i < 44;i++){
        cout<<enc[i];
    }
    //enc = "miniLCTF{TsrevER_gnir33nignE_Is_d1nd_0F_@rt}"
    return 0;
```
得到答案