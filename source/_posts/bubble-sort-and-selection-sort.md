---
title: 'bubble sort and selection sort '
date: 2019-09-18 16:41:06
tags: code心得
categories: 编程实践
---
# bubble sort
```
void bubble(struct card A[],int N){
    for(int i = 0;i < N; i++){
        for(int j = N-1; j >= i ; j-- ){//这里是需要学习的地方，直接让j作为最后，更改循环条件尽可能的减少次数
            if(A[j].value < A[i].value){
                card t = A[j];
                A[j] = A[i];
                A[i] = t;
            }
        }
    }

}
```
# selection sort
```
void selection(struct card A[],int N){
    for(int i = 0; i < N; i++){
            int minj = i;//定义一个minj用来接收所有比较中的最小值元素所在的位置
        for(int j = i; j < N; j++){//选择排序从前往后找，在j循环时直接将j = i带入，另外利用空位转存将要被替代的旧元素的值
            if(A[j].value < A[i].value){
                minj = j;
            }
            card t = A[i]; A[i] = A[minj]; A[minj] = t;
        }
    }

}
```
# 算法稳定性的判断
```
//判断算法的稳定性
bool isStable(struct card C1[],struct card C2[],int N){//新增加一个标志位，观察标志位是否如原先相同就可以得出是否是稳定的结论
for(int i = 0 ; i < N ; i++){
    if(C1[i].suit != C2[i].suit){
        return false ;
    }
}
return true;
}
```