### Algorithm & DataStructure
- C程序设计
- 数据结构（C语言版）
- 算法
- 数据结构与算法分析--c语言描述

##### 二分查找（binary_search）
`arrays`为查找数组
`result`为查找结果
`length`为数组长度

时间复杂度

- 最好结果：1
- 最坏结果：lg(N)
```
int binary_search(int arrays[],int result,int length){
    int begin=0,end=length-1;
    int mid=0;
    while(begin<=end){
        mid=(begin+end)/2;
        if(arrays[mid]==result)break;
        else if(arrays[mid]<result)begin=mid+1;
        else if(arrays[mid]>result)end=mid-1;
    }
    return mid;
}
```
##### 最大子序列和（maxSubSeqSum）
- 时间复杂度:T(N)=O(N3)
```
int MaxSubSeqSum(int arrays[],int length){
    int i,j,k,thisSum=0,maxSum=0;
    for(i=0;i<length;i++){
        for(j=i;j<length;j++){
            thisSum=0;
            for(k=i;k<=j;k++){
                thisSum+=arrays[k];
            }
            if(thisSum>maxSum)maxSum=thisSum;
        };
    }
    return maxSum;
}
```
##### 最大子序列和改进1（maxSubSeqSum）
- 时间复杂度:T(N)=O(N2)
```
int MaxSubSeqSum(int arrays[],int length){
    int i,j,thisSum=0,maxSum=0;
    for(i=0;i<length;i++){//i是子列左端
        thisSum=0;//从arrays[i]到arrays[j]的子序列和
        for(j=i;j<length;j++){//j是子序列右端
            thisSum+=arrays[j];
            if(thisSum>maxSum)maxSum=thisSum;//如果本次求和大于最终结果，更新maxSum
        };
    }
    return maxSum;
}
```
##### 最大子序列和（maxSubSeqSum）--分治法
算法复杂度:T(N)=O(NlgN)
```
int MaxSubSeqSum(int arrays[], int left, int right) {
    int sum = 0;
    if (left == right) {
        if (arrays[left] > 0)sum=arrays[left];
        else sum = 0;
    } else {
        int middle = (left + right) / 2;
        int leftSum = MaxSubSeqSum(arrays, left, middle);
        int rightSum = MaxSubSeqSum(arrays, middle+1, right);
        int finalLeftSum = 0, thisLeftSum = 0;
        for (int i = middle; i >=left; i--) {
            thisLeftSum += arrays[i];
            if (thisLeftSum > finalLeftSum)finalLeftSum = thisLeftSum;
        }
        int finalRightSum = 0, thisRightSum = 0;
        for (int j = middle+1; j <=right; j++) {
            thisRightSum += arrays[j];
            if (thisRightSum > finalRightSum)finalRightSum = thisRightSum;
        }
        sum = finalLeftSum + finalRightSum;
        printf("left sum is %d,right sum is %d\n",finalLeftSum,finalRightSum);
        if (sum < leftSum)sum = leftSum;
        if (sum < rightSum)sum = rightSum;
    }
    return sum;
}
```
##### 线性表的顺序存储
```
typedef struct LinkedList{
    int data[MAXSIZE];//存储数组
    int Last;//游标指针
}List;
List * MakeEmpty(){
    List *Ptrl;
    Ptrl=(List *)malloc(sizeof(List));
    Ptrl->Last=-1;
    return Ptrl;
}
/**T(n)=O(N)*/
int FindElement(int result,List* Ptrl){
    int i=0;
    while(i<=Ptrl->Last&&Ptrl->data[i]!=result)
        i++;
    if(i>Ptrl->Last)return -1;//如果没有找到返回-1
    else return i;
}
void Insert(int result,int i,List *Ptrl){
    int j;
    if(Ptrl->Last==MAXSIZE-1){
        printf("表满");
        return;
    }
    if(i<1||i>Ptrl->Last+2){
        printf("插入位置不合法");
        return;
    }
    for(j=Ptrl->Last;j>=i-1;j--)//倒序移动（注意j>=i-1）
        Ptrl->data[j+1]=Ptrl->data[j];
    Ptrl->data[i]=result;//插入数据
    Ptrl->Last++;//Last指针++
    return;
}
/*T(N)=O(N)*/
void DeleteElement(int i,List* Ptrl){
    int j;
    if(i<1||i>Ptrl->Last+1){//判断位置的合法性（注：链表下标是从1开始的）
        printf("第%d个元素不存在",i);
        return;
    }
    for(j=i;j<=Ptrl->Last;j++)//所有元素前移
        Ptrl->data[j-1]=Ptrl->data[j];
    Ptrl->Last--;
    return;
}
```
##### 线性表的链式存储
```
typedef struct LinkedList {
    int data;
    struct LinkedList *next;
} List;

//求链表长度
int Length(List *Ptrl) {
    List *p = Ptrl;
    int j = 0;
    while (p) {
        p = p->next;
        j++;
    }
    return j;
}

//按序号查找
List *FindKth(int k, List *Ptrl) {
    List *p = Ptrl;//不改变头节点，返回移动指针p
    int i = 1;
    while (p != NULL && i < k) {
        p = p->next;
        i++;
    }
    if (i == k)return p;
    else return NULL;
}

//按值查找
List *Find(int result, List *Ptrl) {
    List *p = Ptrl;
    while (p != NULL && p->data != result)
        p = p->next;
    return p;
}

//构造新的结点，申请空间
//找到链表的低i-1个结点
//修改指针插入结点
List *Insert(int result, int i, List *Ptrl) {
    List *p, *s;
    if (i == 1) {//判断链表为空的时候
        s = (List *) malloc(sizeof(List));
        s->data = result;
        s->next = Ptrl;
        return s;
    }
    p = FindKth(i - 1, Ptrl);//查找第i-1个节点
    if (p == NULL) {
        printf("参数错误");
        return NULL;
    } else {
        s = (List *) malloc(sizeof(List));
        s->data = result;
        s->next = p->next;
        p->next = s;
        return Ptrl;
    }
}

List *Delete(int i, List *Ptrl) {
    List *p, *s;
    if (i == 1) {//检查要删除的是不是第一个结点
        s = Ptrl;//s指向第一个结点
        if (Ptrl != NULL)Ptrl = Ptrl->next;//从链表中删除
        else return NULL;
        free(s);//释放空间
        return Ptrl;
    }
    p = FindKth(i, Ptrl);
    if (p == NULL) {//后项前移
        printf("输入错误");
        return NULL;
    } else if (p->next != NULL) {
        s=p->next;
        p->next = s->next;
        free(s);//清理无用空间
        return Ptrl;
    }
}
```
##### 广义表
```
/*广义表是线性表的推广
 *对于线性表而言，n的元素都是基本单元素
 *广义表中，这些元素不仅可以是单元素也可以是另一个广义表
 **/
typedef struct GNode{
    int Tag;//标识域：0表示结点是单元素，1表示结点是广义表
    union {//子表指针域Sublist与单元素数据域Data复用，即共用存储空间
        int data;
        struct GNode *SubList;
    }URegion;
    struct GNode *next;
}GList;
/*
 * 多重链表中节点的指针与会有多个，但包含两个指针域的链表并不一定是多重链表，比如双向链表
 * 多重链表有广泛的用途，（树，图等相对复杂的数据结构都可以用多重链表存储）
 * */
 
 /*矩阵可以使用二维数组表示，但二维数组表示优缺点（1.数组的大小需要事先确定，2.对于稀疏矩阵将造成大量的存储空间浪费）
  *采用一种典型的多重链表-十字链表来存储稀疏矩阵
  *只存储矩阵非0元素项（结点数据域：行坐标Row，列坐标Col，数值Value）
  *每个结点通过两个指针域，吧同行、同列串起来；
  * 行指针（右指针）Right
  * 列指针（下指针）Down
  **/
 ```
##### 堆栈（顺序存储）数组方式
```
typedef struct{
    int Data[MAXSIZE];
    int Top;
}Stack;
void Push(Stack *stack,int value){
    if(stack->Top==MAXSIZE-1){//数组有界
        printf("堆栈满");
    }else{
        stack->Data[++(stack->Top)]=value;
        return;
    }
}
int Pop(Stack *stack){
    if(stack->Top==-1){//为空检查
        printf("堆栈为空");
        return ERROR;
    } else
    return stack->Data[stack->Top--];
}
```
##### 一个有界数组存储两个堆栈
```
#define MAXSIZE 50
   /*一个有界数组存储两个堆栈，如果数组有空间则执行入栈操作，（一个向右增长，一个向左增长）
    * */
   typedef struct DStack{
       int data[MAXSIZE];
       int Top1;
       int Top2;
   }Stacks;
   void Push(Stacks *stacks,int value,int Tag){
       if(stacks->Top2-stacks->Top1==1){
           printf("堆栈满");return;
       }
       if(Tag==1)
           stacks->data[++stacks->Top1]=value;
       else
           stacks->data[--stacks->Top2]=value;
   }
   int Pop(Stacks *stacks,int Tag){
       if(Tag==1){
           if(stacks->Top1==-1){
               printf("堆栈1空");
               return NULL;
           }else {
               return stacks->data[stacks->Top1--];
           }
       }else{
           if(stacks->Top2==MAXSIZE){
               printf("堆栈2空");
               return NULL;
           }else{
               return stacks->data[stacks->Top2++];
           }
       }
   }
   int main() {
       Stacks *stacks;
       stacks->Top1=-1;
       stacks->Top2=MAXSIZE;//初始化两个堆栈头指针
       return 0;
   }

```
##### 堆栈（链式存储）
```
/*用单向链表表示栈时候，栈Top结点一定是链头结点
 * */
typedef struct Node{
    int value;
    struct Node *next;
}LinkedStack;
LinkedStack * CreateLinkedStack(){
    LinkedStack *stack;
    stack=(LinkedStack *)malloc(sizeof(LinkedStack));
    stack->next=NULL;
    return stack;
};
int isEmpty(LinkedStack *stack){//注意Top结点没有值，只有一个单链表的头指针
    return (stack->next==NULL);
}
void Push(LinkedStack *stack,int value){
    LinkedStack *insertElement;
    insertElement=malloc(sizeof(LinkedStack));//分配内存空间
    insertElement->value=value;//插入的值赋值给结点
    insertElement->next=stack->next;//将已存在链表链接到插入的结点
    stack->next=insertElement;//改变Top结点
}
int Pop(LinkedStack *stack){
    int result;
    LinkedStack *popElement;
    if(isEmpty(stack)){
        printf("链表为空");
        return ERROR;
    }else{
        popElement=stack->next;
        result=popElement->value;
        stack->next=popElement->next;
        free(popElement);//记得释放无用内存空间
        return result;
    }
}
/*中缀表达式如何转换为后缀表达式
 * 从头到尾读取中缀表达式的每一个对象
 * 1.运算数：直接输出
 * 2.左括号：压入堆栈
 * 3.右括号：将栈顶的运算符弹出并输出，直到遇到左括号（出栈不输出）
 * 4.运算符：
 *          若优先级大于栈顶运算符，则压栈
 *          若优先级小于等于栈顶运算符，将栈顶运算符弹出兵书vhu，再比较新的栈顶运算符，直到改运算符大于栈顶运算符优先级为止，然后压栈
 * 5.若个对象处理完毕，则把堆栈中存留的运算符一并输出
 * 堆栈用途：
 *      函数调用及递归实现
 *      深度优先搜索
 *      回溯算法
 * */
```
#####队列（顺序存储）
```
#define MAXSIZE 50
typedef struct {
    int value[MAXSIZE];
    int rear;
    int front;
}Queue;
Queue *CreateQueue(){
    Queue *queue;
    queue=(Queue *)malloc(sizeof(Queue));
    queue->front=0;
    queue->rear=0;
    return queue;
}
void AddQueue(Queue *queue,int value){
    if((queue->rear+1)%MAXSIZE==queue->front){
        printf("queue is full");
        return;
    }else {
        queue->rear=(queue->rear+1)%MAXSIZE;
        queue->value[queue->rear] = value;
    }
}
int IsEmpty(Queue *queue){
    if(queue->rear==queue->front)return 1;
    else return 0;
}
void OutQueue(Queue* queue,int *value){
    if(IsEmpty(queue)){
        printf("Queue is empty");
        return;
    }else{
        queue->front=(queue->front+1)%MAXSIZE;
        *value=queue->value[queue->front];
    }
}
```
#####队列（链式存储）注意
```
typedef struct Node {
    int value;
    struct Node *next;
} QNode;
typedef struct {
    QNode *rear;
    QNode *front;
} Queue;

void InitQueue(Queue **queue) {
    QNode *p;
    p = (QNode *) malloc(sizeof(QNode));
    p->next = NULL;
    (*queue)->front = p;
    (*queue)->rear = p;
}

void InQueue(Queue *queue, int value) {
    QNode *InElement;
    InElement = (QNode *) malloc(sizeof(QNode));
    InElement->value = value;
    InElement->next = NULL;
    queue->rear->next = InElement;
    queue->rear = InElement;
}

int IsEmpty(Queue *queue) {
    if (queue->rear = queue->front)return 1;
    else return 0;
}

void OutQueue(Queue *queue, int *value) {
    QNode *OutElement;
    if (IsEmpty(queue)) {
        printf("queue is empty");
        return;
    } else {
        OutElement = queue->front->next;
        queue->front->next=OutElement->next;//指针头结点指向下一个结点(pspspspsps)
        *value=OutElement->value;
        free(OutElement);
        if(IsEmpty(queue)){//出队列后如果队列为空则置为空队列（有必要么？）
            queue->front=queue->rear;
        }
    }
}
```