---
layout: post
#标题配置
title:  Online Coding
#时间配置
date:   2019-06-02
#大类配置
categories: Interview
#小类配置
tag: Online Coding
---

* content
{:toc}

### 1. 一个大小为N的数组，里面的值代表的是股价，求一次买入卖出所能够求得最大收益？如果是两次买入卖出呢？

	def best_time(nums):
	    l1 = len(nums)
	    if l1 == 1:
	        return 0
	    max = 0
	    min = nums[0]
	    for i in range(l1):
	        if nums[i] < min:
	            min = nums[i]
	        elif max < nums[i]-min:
	            max = nums[i] - min
	    return max
	 
	in_nums = [7,1,5,3,6,4]

### 2. 从日志中找出IP出现次数超过1024次的恶意IP

	struct node{
		int num;
		int index;
	};
	struct node array[MAXN];
	int top = -1;
	for(int i = 2;number != 1;i++){
		if(number % i == 0){
			array[++top].num = i;
			while(number % i == 0){
				array[top].index++;
				number /= i;
			}	
		}
	}

### 3. 一个10T的文本，一个10M的文本，从大文本中找出与小文本中相似度大于80%的文本

### 4. 删除链表中倒数第K个节点，如果是个带环的链表呢？

### 5. 最大合数的质子分解

### 6. 计算从出生到达18岁生日所经过的总天数。

    #include<stdio.h>
    int main(){
    	int n,i,year,month,day,t,y;
        scanf("%d",&n);
        for(i=1;i<=n;i++){
        	scanf("%d-%d-%d",&year,&month,&day);
        	if(month==2&&day==29) //4年过一次生日{
        		printf("-1\n");
        	}
        	else{
        		t=0;
        	if(month>=3) //大于二月，后一年的天数决定一岁的天数{
        		for(y=year+1;y<=year+18;y++){
        			if((y%4==0&&y%100!=0)||y%400==0){
        				t=t+366;
        			}
                   	else{
                   		t=t+365;
                   	}
                }
            }
            else if(month<=2)//小于等于二月，这一年的天数决定一岁的天数{
                for(y=year;y<=year+17;y++){
                    if((y%4==0&&y%100!=0)||y%400==0){
                        t=t+366;
                    }
                    else{
                        t=t+365;
                    }
                }
            }
            printf("%d\n",t);
        }
    }




