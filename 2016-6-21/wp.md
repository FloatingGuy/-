## 新年大礼包~ wp
。。。半年过去了，才做完，，，有点惭愧。。。
人家入门的基本题目、、、、我还是太菜了
好好学习天天向上~

不过感觉学到了好的新的姿势

## babyCrack
用任意 .net 工具打开就可以看到源码
		private void button1_Click(object sender, EventArgs e)
		{
			bool flag = false;
			Config.user = this.textBox1.Text;
			string user = Config.user;
			string text = "hctf{bABy_CtsvlmE_!}";
			int num = text.CompareTo(user);
			if (num == 0)
			{
				flag = true;
			}
			if (flag)
			{
				MessageBox.Show("good !!!");
			}
		}
明显是 hctf{bABy_CtsvlmE_!}

## CrackMe2

寻找字符串。。发现在sub_411A20 为重要的函数

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/cm2_1.png)

但是经过加了花指令 无法 F5 编译

手工去花

去完后就变简单了

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/cm2_2.png)

就是简单的比较字符串



## elf1
因为是linux题目 动态调试有点难。。。
做了好久才做出来。。

> 要掌握 多动态调试的姿势

IDA 载入 查找字符串 看到 The flag is flag{
明显是在这个附近双击 查看附近的代码

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/elf1_1.png)


有一个gets 和 输入的函数，估计就是这个就是重点了。
![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/elf1_2.png)

静态看的不是很舒服， 可以得到的信息点是 while(v3!=15) 可以知道字符串的长度为15
至于sub_8048519 这个函数，，点进去看了，实在看不下去就不看了。。

从前两个 if语句也可以得到 输入的字符串应该是属于(96,122]这个范围的，正好全部是小写字母

拿到 kali 的 逆向工具去调试一下，，输入abcdefghijklmno
在 V8=0的地方下断点  运行到这个地方，，看堆栈

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/elf1_4.png)


在堆栈这里看到处理之后的字符串变成 nopq……zab 可以发现字母都偏移了13位，，往左往右偏移都是13位。。可以得到这个 sub_8048519 的主要目的应该就是 判断是不是属于 小写字母 并且偏移13位

得到这样的推断后 继续往下分析 IDA 

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/elf1_3.png)

	if ( *(a1 + v8 - 88) - *(a1 + v8 - 89) != *(a1 + 4 * v8 - 76) )
	        break;
这个就是重点的 判断函数了  从edb中可以推断出 重新生成的字符串是存放在 a1 + v8 - 89 为起点的内存中 然后判断 

找到 两两比较的差。

然后就 在重 return sub_8048519(a1) == 'b'; 得到 首位会是一个'b' 那好 直接算一下出结果  

	char a[100];
	__int8 b[]={0xff,0x11,0xf5,3,0xf8,0x5,0xe,0xfd,0x01,0x6,0xf5,0x6,0xf8,0xf6};
	for(int i=0;i<=13;i++)
		printf("%2x ",(unsigned __int8)b[i]);
	printf("\n");
	a[15]='\0';
	a[0]='b';
	for(int i=1;i<=14;i++)
		a[i]=a[i-1]+b[i-1];
	puts(a);

	printf("\n");
	int i=0;
	while(a[i]!='\0')
	{
		a[i]=(a[i]-'a'+13)%26+'a';
		i++;
	}
	puts(a);

因为 最后还有一个 sub_8048519(a1) 所以 这个字符串要进行偏移处理。


#elf3

坑逼一样的题目，，。  

难点。程序自校验，包括断点。

打开程序。找到输出，Please input your flag: 下方的代码

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/elf3_3.png)


看起来很简单的样子，直接看汇编，一遍动态调试一边观察。

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/elf3_2.png)


看到这里有一个循环，，，感觉并没有用，，直接在后面下断点就 过去了

然后就栽在这里了。。搞了半天没搞出来。。。

一个断点就会造成那个函数判断之后出来的 值的变化。。
![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/elf3_1.png)

这里 eax就是真实的 值

然后下面进行一堆异或就能够出flag的。。


## re200

难点 C++写的ELF 一定需要动态调试

查找字符串来到这里，

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/re200_2.png)


关键跳转 上面有 test al,al 就是call rax 里是不是正确

动态调试跟入，， 发现与7比较。。。就是用户名的长度一定是要7

这个判断对接下来的东西并没有什么影响，只是判断用户名的长度而已

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/re200_1.png)

这里和上面也差不多 因为是要跟入寄存器 所以一定要动态调试才能看到

跟入后就简单了。

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/re200_3.png)

发现这里是将输入的进行 -4 然后 异或 32处理

然后在处理完的函数下面可以找到比对的 字符串
>75 0C 51 7B 1F 7D 4A 7B 02 6E 41 7D 0D 0D 55 7B 60 0C 7B 65 50
就是这个，，然后写下判断的东西 flag轻松获得


#Re300

这一题有点点小尴尬，，水水的题目因为系统的原因。。在OD载入 没经过解密的语句直接到0x401000 位置，，代码都没经过解密，是不能够运行的。

这个题目貌似还考验了 TLS 反调试，但是在强大的 吾爱破解OD面前 什么都没有用

讲讲这个算法。用winhex在进程中查看文件运行之后的 .text 代码段 与文件中的.text段进行比较 发现 最后他的解密方式很简单就是类似 0xab--> 0xba
导致不能够静态编译 一定要解密后才能够静态分析

好啦来讲这个用户名和注册码把

先到这里

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/re300_1.png)

可以看到这里是判断用户名长度为8位

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/re300_2.png)

这里有判断是不是这8位都是小写的字母
![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/re300_3.png)

这里和下面数据窗口中  每间隔 0xb 取一个字节  然后一共取8个 
正好合起来是 syclover  不过猜一下也应该猜到了

后面的注册码 有点意思，，大致的思路就是 讲输入的字母进行 0xab --> 0xba 这样的加密， 然后再与 0x40 进行异或 

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/re300_4.png)

最后得到的字符串再和 这里紫色部分 每隔三个进行比较是否相同（3V...）

就写下具体的思路 最后就不求出来了

# re
把文件拉到kali 发现是一个压缩包
解压一下，放到IDA里面看一下 发现乱的。。
发现UPX字样，怀疑是在linux下加壳了
讲文件在kali 命令行下 输入 UPX -d encoder
这样就可以直接脱壳了。
然后再到IDA里面看，看起来比较复杂。所以直接 用kali自带的edb调试

调试中会自动退出，Ida 里看到是因为 ptrace() 在作怪，，直接绕过他 分析函数

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/re_1.png)

第一轮加密执行的是 x<<3|x>>5的操作

第二轮 

![](https://raw.githubusercontent.com/GoldsNow/-/7df9cc11fef75fb7b65bf73058ad624d1375f4a0/2016-6-21/re_2.png)

进行的是和一个数组异或。

写一下解密程序
		
	char a[]={"L3mon-is-so-handsome_:D"};
	int flag[]={0x67,0x10,0x36,0xfe,0xbf,0xcf,0x0b,0xea,0x46,0x08,0x1c,0xd7,0xf9,0xf8,0xf7,0xfd,0xea,0xf6,0x97,0x46,0x24,0x81,0x37,0x2f,0x48,0x66,0x4c};
	for(int i=0;i<28;i++)
	{
		char c=flag[i]&0xff;
		c=c^a[i%23];
		c=((c&0xff)<<5|(c&0xff)>>3)&0xff;
		printf("%c",c);
	}
	return 0;

