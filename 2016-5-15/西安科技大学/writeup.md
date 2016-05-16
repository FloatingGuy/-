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