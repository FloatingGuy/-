西安科技大学 内部赛的三道题目

题目打包 [点击下载](https://github.com/GoldsNow/-/raw/5959936765c68a20e2231c1f671304f39b17ca60/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/%E9%A2%98%E7%9B%AE.rar)

## TASK1
题目有点意思 但是还是有点点水

OD因为干扰的函数太多了 太麻烦了 就不用他了
IDA直接分析。  主函数 中找到在放完歌曲之后的函数

![](https://raw.githubusercontent.com/GoldsNow/-/9b6e84ed3b9fa434a3c0b525ec55654a316bb90c/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/4.png)

重点函数 sub_401060 重点字符串 byte_406550

![](https://raw.githubusercontent.com/GoldsNow/-/9b6e84ed3b9fa434a3c0b525ec55654a316bb90c/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/5.png)

恰巧只有怎么一个函数有用，还是简单的
写个c跑一下

	#include<stdio.h>
	#include<string.h>
	int main()
	{
		char a[]={"Bwqs_2_Ash_I_LighQHT"};
		memset(flag,0,sizeof(flag));
		for(int i=0;i<20;i++)
		{
			if((a[i]<97||a[i]>122)&&(a[i]<65||a[i]>90))
				flag[i]=a[i];
			else
			{
				for(int c=60;c<130;c++)
				{
					if((c<97||c>122)&&(c<65||c>90))
						continue;
					int d=0;
					d=( c<97?0:0x20)+(c - (c < 97 ? 0 : 0x20) + 1951) % 26 + 65;
					if(d==a[i])
						flag[i]=c;
				}
			}
			//for(int j=0;j<20;j++)
			//	printf("%c",flag[j]);
			printf("\n");
		} 
		puts(flag);
		return 0; 
	} 
秒出flag



## TASK2
简单逆向。。。
通过查找字符串发现有 
![](https://raw.githubusercontent.com/GoldsNow/-/49ed5bd40f8342595614a184eef133d266ecff49/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/1.png)
点击进去相关的反汇编语段
![](https://raw.githubusercontent.com/GoldsNow/-/49ed5bd40f8342595614a184eef133d266ecff49/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/2.png)
在离这个最近的一个CALL函数进行步入跟踪
![](https://raw.githubusercontent.com/GoldsNow/-/49ed5bd40f8342595614a184eef133d266ecff49/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/3.png)
出现的这一个函数是比较复杂的。结合IDA来进行分析一下
总结出来 这个的转化意思是

	d=(a[(2*c-0x209)%al]^c)%0xff+flag[c-0x20a]
对这个多一些逆向程序 很容易，因为就只是加减处理

	#include<stdio.h>
	#include<string.h>
	int main()
	{
		char a[100]={"can_you_fee1_my_w0r1d_sh0w_me_your_10ve"};
		int al=strlen(a); //al代表a[]的字符串长度。 
		int s[26]={0xD8,0xEB,0xDC,0xd1,0xfb,0x88,0x9b,0xd6,0xc2,0xbe,0xa1,0x7a,0xd1,0xbc,0x9e,0xbf,0xa7,0xb3,0x5c,0xa4,0x7a,0x8f,0x63,0xd3,0xaf,0xd4};
		char flag[100];
		int c=0x209,d=0x1a;
		for(int i=0;i<26;i++)
		{
			//d=(a[(2*c-0x209)%al]^c)%0xff+flag[c-0x20a];
			flag[i]=s[i]-((a[(2*c-0x209)%al]^c)%0xff);
			c++;
			d--;
		}
		printf("%s",flag);
		return 0;
	}
最后贴上渣渣的程序。
最后跑一下直接出flag！


## TASK3

OD打开 搜索一下字符串，然后发现有这么几条字符串比较可疑。

![](https://raw.githubusercontent.com/GoldsNow/-/1f00d3609c9efa4100097ff71c6fd8dbfa2f82a0/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/9.png)

分别跟入会会发现比较可疑
![](https://raw.githubusercontent.com/GoldsNow/-/1f00d3609c9efa4100097ff71c6fd8dbfa2f82a0/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/8.png)

然后我强制让这一段代码运行了一下，然后就会输出GETIT这样的字符串。
说明在这个附近有判断字符串是否正确的语句

![](https://raw.githubusercontent.com/GoldsNow/-/1f00d3609c9efa4100097ff71c6fd8dbfa2f82a0/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/6.png)

这个程序比较可疑，就是会有新的进程出来。


程序调试到这里就很难再继续下去了 转到IDA来分析 然后再来到OD来看
根据上面找到的地址，然后直接去查看IDA上的相关函数。然后直接分析代码。

![](https://raw.githubusercontent.com/GoldsNow/-/1f00d3609c9efa4100097ff71c6fd8dbfa2f82a0/2016-5-15/%E8%A5%BF%E5%AE%89%E7%A7%91%E6%8A%80%E5%A4%A7%E5%AD%A6/7.png)


	附上程序代码
	#include<string.h>
	#include<stdio.h>
	
	int main()
	{
		char a[]={"JIKE1202"};
		int al=strlen(a);
		char b[]={"Fiac>d;=ImlNNfQPR[lPJ?hsIEmrN;=H"};
		char flag[32];
		memset(flag, 0 ,sizeof(flag));
		for(int i=0;i<32;i++)
		{
			int v2=i%al; 
			for(int j=32;j<130;j++)
			{
				if((j<97||j>122)&&(j<65||j>90)&&j!='_'&&j!='{'&&j!='}')
					continue;
				char c=j;
				if(b[i]== ((c & 0x20 | a[v2]) ^ (c << (a[v2] & 1))) % 65 + 58)
					printf("%c",j);
			}
			printf(" ");
		}
		return 0;
	}

通过这个程序的输出可以发现这个题目也是有点点小问题的，能够输出多个解，这个也是没有办法的事情。对于相应的函数是很有可能能够得到多个解的。

