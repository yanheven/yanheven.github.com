---
layout: post
title: 合并排序
category: coding
description: 合并排序

---

Author:[Hyphen](http://weibo.com/344736086)


合併排序法

{         持續將一堆沒有順序的資料分割成兩半，直到每一份都只有一筆資料

{         將分割完畢的資料兩兩合併

 


 

e.g.

12    5     9     15     7

                 

                 12    5    9                15      7

 

               12    5         9            15         7

 

              12      5

 

 

 

	#include<stdio.h>

	int a[]={12,5,9,15,7};

	int b[5];

	void merge(int lo, int m,int hi)

	{

     	int i, j, k;

    	// copy both halves of a to auxiliary array b

    	for (i=lo; i<=hi; i++)

      	  b[i]=a[i];

     	  i=lo; j=m+1; k=lo;

   	 // copy back next-greatest element at each time

    while (i<=m && j<=hi)

        if (b[i]<=b[j])

            a[k++]=b[i++];

        else

            a[k++]=b[j++];

 

    // copy back remaining elements of first half (if any)

    while (i<=m)

        a[k++]=b[i++];

	}

 

	void mergesort(int lo, int hi)

	{

    if (lo<hi)

    {

        int mid=(lo+hi)/2;

        mergesort(lo, mid);

        mergesort(mid+1, hi);

        merge(lo, mid, hi);

    }

	}

 

	int main()

	{

     int lo,hi,i;

     lo=0;

     hi=4;

     mergesort(lo,hi);   

     for(i=0;i<5;i++)

        printf("%d ",a[i]);

     printf("\n");

     getche();

     return 1;

	}