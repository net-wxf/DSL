# 队列

### 普通初始化和定义，入队出队

```javascript
#include <stdio.h>
#include "../include/Queue.h"

int main(){

    SqQueue q;
    InitQueue(q);
    int e=0;
    for(int i=0;i<6;i++){
        EnQueue(q,i);
    }

    PrintQueue(q);
  
    printf("%d %d",q.data[q.front],q.data[q.rear-1]);
    //PrintQueue(q);
    printf("\n");
    //if(!GetHead(q,e)){return 0;}
    if(!DeQueue(q,e)){return 0;}
    printf("%d ",e);
    printf("\n");
    printf("%d %d",q.data[q.front],q.data[q.rear-1]);
   
    printf("\n");
    if(!EnQueue(q,e)){return 0;}
    PrintQueue(q);
    return 0;


}
```

![image.png](https://res.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/d7769351-3895-4b31-847d-b15dae9fb2e1/b2ea360b-4d95-4d7e-9e44-eab2f9b74cf3)

```javascript
#include <stdio.h>
#define MaxSize 20

//普通队列的顺序存储
typedef struct{
    int data[MaxSize];
    int front,rear;
}SqQueue;

void InitQueue(SqQueue &q){
    q.front=0;
    q.rear=0;
}

bool EnQueue(SqQueue &q,int e){
    if(q.rear==MaxSize-1) return false;
    //如果队满，因为数组从0开始
    q.data[q.rear++]=e;
    return true;

}

bool DeQueue(SqQueue &q, int &e){
    if(q.rear==q.front) return false;

    e=q.data[q.front++];
    return true;

}

bool EmptyQueue(SqQueue q){

    if(q.rear==q.front) return true;
    return false;
}


bool GetHead(SqQueue q, int &e){
    if(EmptyQueue(q)) return false;
    e=q.data[q.front];
    return true;

}

void PrintQueue(SqQueue q){
    if(!EmptyQueue(q)){
        for(int i=q.front;i<q.rear;i++){
        printf("%d ",q.data[i]);
         }
    printf("\n");
    return;
    }
    printf("The SqQueue is empty!\n");
}
```

### 循环队列

只是在插入删除时的判断语句以及操作语句不一样

```javascript
#include <stdio.h>
#include "../include/Queue.h"

int main(){

    SqQueue q;
    InitQueue(q);
    int e=0;
    for(int i=0;i<5;i++){
        EnQueue(q,i);
    }

    PrintQueue(q);
    if(!DeQueue(q,e)){printf("The queue is empty\n");return 0;} 
    printf("%d %d size is %d\n",q.data[q.front],q.data[q.rear-1],SizeQueue(q));
    if(!DeQueue(q,e)){printf("The queue is empty\n");return 0;} 
    printf("%d %d size is %d\n",q.data[q.front],q.data[q.rear-1],SizeQueue(q));
    if(! EnQueue(q,e)){printf("The queue is full\n");return 0;} 
    PrintQueue(q);
    printf("%d %d size is %d\n",q.data[q.front],q.data[q.rear-1],SizeQueue(q));


    return 0;


}
```

![image.png](https://res.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/d7769351-3895-4b31-847d-b15dae9fb2e1/e7da3a65-a40d-4e8f-991e-6bf17b58b1de)

```javascript
#include <stdio.h>
#define MaxSize 6

//循环队列的顺序存储
typedef struct{
    int data[MaxSize];
    int front,rear;
}SqQueue;

void InitQueue(SqQueue &q){
    q.front=0;
    q.rear=0;
}

bool EnQueue(SqQueue &q,int e){
    if((q.rear+1)%MaxSize==q.front) return false;
    //如果队满，因为数组从0开始
    q.data[q.rear]=e;
    q.rear=(q.rear+1)%MaxSize;
    return true;

}

bool DeQueue(SqQueue &q, int &e){
    if(q.rear==q.front) return false;

    e=q.data[q.front];
    q.front=(q.front+1)%MaxSize;
    return true;

}

bool EmptyQueue(SqQueue q){

    if(q.rear==q.front) return true;
    return false;
}


bool GetHead(SqQueue q, int &e){
    if(EmptyQueue(q)) return false;
    e=q.data[q.front];
    return true;

}

void PrintQueue(SqQueue q){
    if(!EmptyQueue(q)){

        while(q.front!=q.rear){ //只要他两不等
            printf("%d ",q.data[q.front]);
            q.front=(q.front+1)%MaxSize;
        }
        printf("\n");
        
    
        return;
    }
    printf("The SqQueue is empty!\n");
}

int SizeQueue(SqQueue q){
    return (q.rear-q.front+MaxSize)%MaxSize;
}
```


