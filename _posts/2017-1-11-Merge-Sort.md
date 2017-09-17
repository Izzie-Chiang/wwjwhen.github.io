---
layout: post
title: Merge-Sort Templete
---

<p class="meta">11 January 2017 - Beijing</p>


所谓归并排序，就是递*归*划分，然后按序合*并*


~~~~
void mymerge(int a[], int low, int mid, int high)
{
    int *temp = new int[high - low + 1];
    int i = low, j = mid + 1, k = 0; // j begins from mid + 1
    while(i <= mid && j <= high)
    {
        if(a[i] < a[j])
        {
            temp[k++] = a[i++];
            orderWise += high - j + 1;   // 计算顺序数
        }
        else{
            temp[k++] = a[j++];
            reverseOrder += mid - i + 1; // 计算逆序数
        }
    }

    while(i <= mid)
        temp[k++] = a[i++];

    while(j <= high)
        temp[k++] = a[j++];

    for(int i = 0; i < k; i++)
    {
        a[low + i] = temp[i];
    }
    delete[] temp;
}

void myMergeSort(int a[], int low, int high)
{
    if(low < high)
    {
        int mid = (low + high) / 2;
        myMergeSort(a, low, mid);
        myMergeSort(a, mid + 1, high); //begin from mid + 1
        mymerge(a, low, mid, high);
    }
}
~~~~
{: .language-c++}

![mergesort1](/wwjwhen_blog/images/merge-sort.png)

![mergesort2](/wwjwhen_blog/images/merge-sort2.png)

从上面的代码可以看出其实非常像是二叉树的遍历过程，anyway, just a templete, remember it~

