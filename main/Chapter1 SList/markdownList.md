# 单链表

| Title                 | Status      |
| --------------------- | ----------- |
| 26/5/7 完成第一次学习 | Done        |
| 5/8 继续写代码        | In Progress |

### 初始化，求表长度

当你真正理解了L这个指向头结点的指针作用时，在面对没有头结点的链表也会操作的，不过首个结点需要特殊判断。

**所以说，有了头结点，对任何结点的操作都是同样的，不需要给首元节点特殊化。**

### 头插法建立单链表的误区：

”区分L指针本身和头结点的关系“

采用头插法建立单链表时，活动结点s指向新的结点，而头结点L指向首个结点。关键的交换一步是s->next=L->next; L->next=s; 我不明白，s->next代表s指向结点的next指针域，它应该指向首节点，而首节点的地址是L指向的，应该是s->next=L; L=s;

我的误区在于：将L这个指针本身看作了指向首元节点的头结点，其实他不是，**它L指向的结点才是头结点**

L作为指针，本身是占据64bit的大小的空间而已，它在链表初始化之前，都是“野指针”。知道初始化malloc后，将申请到的新的一个LNode类型的结点所在地址，赋值给了L，**此时L指向头结点LNode. 所以，L称之为头结点指针，而不是头结点本身。L→才是头结点本身。**

并且，此时的L→next 代表 头结点的next指针域，这里用了“指针运算符→，自然明白它隐含的信息”。

既然如此，每一个新加入的结点，都应该由“L→next=s"指向，即头结点的next指针域。而不是L本身.

"L=s"是，表示指针L指向了s指向的新节点，导致原来的头结点LNode没有指针指向它了。错误是：更改了指向头结点的L指针，但是没有更改头结点的Next指针域。

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/6dbec75c-1507-4070-a090-93836e8dd3b1)

```javascript
typedef struct LNode{
    int data;//数据域
    struct LNode* next;//指针域
}LNode, *LinkList; //结构体变量/指针别名

bool InitList(LinkList &L){//初始化函数
    L=(LNode *)malloc(sizeof(LNode));//地址赋值给了L的next指针域，错了
    //指针可以直接赋值
  //这里开始L->都是对头结点的操作，和指针L本身无关
    L->data=0;
    L->next=NULL; 
    //这里都用指针运算符，操作的是L指向结点的成员变量
    return true;
}

LinkList L;//此时得到的是一个64bit的指针大小内存空间，它指向的类型是结构体
初始化我误以为是将LNode 一个地址赋值给了L的指针域，错了，因为L不是结构体变量，没有指针域
只有本身长64bit存地址的空间
```

### 头插法代码，以及显示代码

```javascript
LinkList List_HeadInsert(LinkList &L){//头插法构造单链表，最后返回单链表头结点的指针，
    //主函数中L=LinkList(L);意味着两个指针赋值，指向它头结点指向的位置
    LNode *s; //活动的节点指针
    int x[4]={1,2,3,4};// 节点数据域的值
    //L已经被初始化后，无需再初始化
    for(int i=0;i<4;i++){
        s=(LNode *)malloc(sizeof(LNode));//创建新结点
        //s本身是一个结构体指针变量，但是创建单链表需要创建结点，故用malloc，s作为中间人，负责传递
        s->data=x[i];
        s->next=L->next; // s->next=L;是错的，因为头指针是单链表之外的，应该指向头结点，故头结点的地址赋值给s指向的新结点指针域
        L->next=s;
    }
    return L;

}

void PrintList(LinkList L){
    LNode *p;//活动结点指针
    p=L->next;// 将头节点地址赋值给p
    for(int i=0;i<4;i++){
        printf("%d ",p->data);
    //链表怎么得到下一个结点的值？
    //p作为指针，指向了首元节点，p->next是第二结点的位置，故p=p->next;即可
    //将第二个结点的地址赋值给p，p指向了第二个结点
        p=p->next;
    }
    printf("\n");
}

#include <stdio.h>
#include "../include/LinkList.h"

int main(){
    LinkList L;
    InitList(L);
    L=List_HeadInsert(L);
    PrintList(L);
    return 0;
}
```

输出结果：

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/59110366-880e-4c4e-a32d-f96328edb854)

### 尾插法建立单链表：

用malloc 建立的结点，包括结构体的所有内容，有next，有data。而开头的(LNode *)只是把首地址单独拿出去了罢了。

```javascript
LinkList List_TailInsert(LinkList &L){//尾插法，顺序建立单链表
    //当然，都是从0个元素开始建立的
    LNode *s;// 活动结点指针，指向插入结点
    LNode *r;// 尾指针，这样操作比较方便

    r=L;//r尾指针，最初指向头结点
    
    int x[4]={0,1,2,3};
    for(int i=0;i<4;i++){
        //s初始化放在循环里，每次都申请一个新的空间
        s=(LNode *)malloc(sizeof(LNode)); //指向待插入结点，
        s->data=x[i];//新插入结点赋值
        r->next=s;//尾指针指向的结点next域指向s
        r=s;//移动r的方法，就是直接赋值指针
    }

   r->next=NULL;// 如果不设置会导致length的while语句总时判断出错，无法跳出循环，不是空的
    return L;
}
只需要两个指针就好，一个活动，一个尾指针，每次插入，都根据尾指针来插入
```

### 求长度

```javascript
int Length(LinkList L){//求单链表的表长函数
    //为了防止每次操作都要重新确立表头的位置
    //头指针L不移动，创建新指针p作为活动指针
    LNode *p=L;//p指针指向L指向的地址，即头结点
    //指针和指针相互赋值，赋值的是存放的地址
    int i=0;
    while(p->next != NULL){ //只要p指向节点的Next不为空，就++
        i++;
        p=p->next;
    }
    return i;

}
```

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/6b0ea00d-ef6f-4fe2-a9fb-cb4729e90de2)

### 按序号查找结点

```javascript
int i=3;// 待查找的逻辑序号
    LNode* p=GetElem(L,i);
    printf("查找序号数据域:%d\n",p->data);


LNode *GetElem(LinkList L,int i){//i是序逻辑号，按序号查找
    //不判断表空，以及i不合法的  默认0<i<L.length
    LNode* p=L;
    int j=0;
    while(p->next!=NULL && j<i)//判断是否为第i个结点，并且j<i 
    {
        p=p->next;
        j++;
    }
    return p;
}
```

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/03a792d7-72ed-4d47-b743-3dcdc5518eb7)

### 按值查找结点

```javascript
LNode* q=LocateElem(L,2);
    printf("查找数据域的结点:%d\n",p->data);


LNode *LocateElem(LinkList L,int e){ //按值查找

    LNode *p=L;
    while(p->next!=NULL && p->data!=e){
        p=p->next;
    }
    //bug 是查找到最后一个结点就返回
    return p;


}
```

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/931556ea-7b3b-4389-8a16-ba3c67f11a51)

### 单链表的插入

```javascript
int e=8;
    if(! ListInsert(L,2,e)){
        printf("False!\n");
        return 0;
    } 
    PrintList(L);

//后插法
bool ListInsert(LinkList &L, int i,int e){//待插入的位置i，和数据e
    LNode* p=L;
    //首先将p定位到i-1的位置上
    //默认i的位置合理
    int j=0;
    while(p->next!=NULL && j<i-1){ //定位到i-1
        p=p->next;
        j++;
    }
    if(p==NULL) return false; //如果i的位置不合法，最后一个也是空，则返回假
    LNode* s=(LNode *)malloc(sizeof(LNode));
    s->data=e;
    s->next=p->next;//关键的就着两步
    p->next=s;
    return true;

  /*前插法，相对p的前面*/
    while(p->next!=NULL && j<i){ //定位到i
        p=p->next;
        j++;
    }
    if(p==NULL) return false; //如果i的位置不合法，最后一个也是空，则返回假
    LNode* s=(LNode *)malloc(sizeof(LNode));
    s->data=e;
    s->next=p->next;
    p->next=s;

    int temp;
    temp=s->data;
    s->data=p->data;
    p->data=temp;

    return true;

}
```

### 单链表的删除

```javascript
if(! DeleteList(L,3)){
        printf("False!\n");
        return 0;
    } 
    PrintList(L);

bool DeleteList(LinkList &L,int i){
    //首先检查i的合法性，
    LNode* p=L;
    int j=0;
    while(p->next!=NULL && j<i-1){//p定位到 i-1
        p=p->next;
        j++;
    }
    while(p==NULL) return false;
    LNode *s=p->next;// s指向删除的i结点，准备释放
    p->next=p->next->next;
    free (s);
    return true;

}
```

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/c2f1151a-bfed-4584-aa6c-97fea2316c5c)

### 打印输出调错

```javascript
LNode *p=L; //p指向头结点
   while(p->next!=NULL){
        printf("%d ",p->data);
        p=p->next; //不能交换顺序。
   }
上述代码导致多显示了一个0，少显示了3，为什么？

分析：p此可和L相同，都指向头结点，此可链表一共有 0，1，2，3 一共4个数据
p->next；//表示头结点的next指针域，
p->data;// 表示头结点的data域，初始化为0，
第一次运行：
输出头结点 0， 第一个结点0，第二个结点1，第三个结点2， 此可执行完输出第三个
p指向第四个，但是循环是p->next, 所以第四个结点导致跳出循环

更改1：
while 判断改成->  p!=NULL,得到图2，
分析，多输出了头结点的数据域

更改2：
printf 更改为 p->next->data;
分析：虽然输出了正确的结果，但是循环出现了问题
这段代码等同于：输出p指针指向结点的下一个结点的数据
导致，每次都是提前输入下一个，等到第三个结点时 p->next->data表示提前输出第四个结点data
则p=p->next, 此时p指向第4个结点，本身不为空，while 继续循环
由于第四个结点next为空，所以此时的 p->next->data是NULL - >data 就错了
本来跳出循环的条件是p本身为空指针

更改3：
while 条件改为p->next!=NULL

FINISHINIG！

   LNode *p=L; //p指向头结点
   while(p->next!=NULL){
        printf("%d ",p->next->data);
        p=p->next; //不能交换顺序。
   }
```

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/100c321f-67fc-baaf-4fd9-b278930ab682)

图1

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/61223bf9-bae6-b9de-d57d-49d1892fc7f0)

图2

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/accb4db6-1ada-ed5b-3abd-3b368c73472f)

图3

![image.png](https://resv2.craft.do/user/full/75cbb767-cd2a-0f76-5994-8dbdbfa83df2/doc/8bcfa983-a727-4932-9c3c-d59e2dfa05fe/7880a554-35e6-4456-98d8-adabf63d92cf)

图4（输出正常）

?descriptionFromFileType=function+toLocaleUpperCase()+{+[native+code]+}+File&mimeType=application/octet-stream&fileName=单链表.md&fileType=undefined&fileExtension=md
