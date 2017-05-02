###Algorithm & DataStructure
- C程序设计
- 数据结构（C语言版）
- 算法
- 数据结构与算法分析--c语言描述

#####二分查找（binary_search）
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
#####最大子序列和（maxSubSeqSum）
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
#####最大子序列和改进1（maxSubSeqSum）
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
#####最大子序列和（maxSubSeqSum）--分治法
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
#####线性表的顺序存储
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
#####线性表的链式存储
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