# 伙伴分配器(Buddy system)的极简实现
#### reference [wuwenbin](https://github.com/wuwenbin/buddy2)

## Wiki:
**分配内存：**  
- 寻找大小合适的内存块（大于等于所需大小并且最接近2的幂，比如需要27，实际分配32）  
    1. 如果找到了，分配给应用程序  
    1. 如果没找到，分出合适的内存块  
        1. 对半分离出高于所需内存大小的空闲内存块
        1. 如果分到最低限度，分配这个大小
        1. 回溯到步骤（寻找合适大小的内存块）
        1. 重复该步骤直到找到一个合适的块或遍历全部可用内存空间
    1. 否则，提示无合适的内存块（TO BE DONE）

**释放内存：**  
- 释放指定的内存块  
    1. 检查相邻内存块是否释放
    1. 如果相邻内存块以释放，合并两个内存块
    1. 重复以上步骤直至无可以继续合并的内存块

## 整体思想：  
通过一个数组形式的完全二叉树来监管内存，二叉树的节点用于标记相应内存的使用状态，高层节点对应大的块，低层节点对应小的块，再分配和释放中通过这些节点的标记属性来进行块的分离合并

## 数据结构：
```
struct buddy2 {
  unsigned size;
  unsigned longest[1];
};
```
size表明管理内存的总单元数目，longest作为二叉树的节点标记，表明所对应的内存块的空闲单位

## 内存分配：
内存分配函数中，输入参数是分配器指针和需要分配的内存大小，返回值是内存块索引。内存分配函数首先将size上调到2的幂次大小，并检查是否超过可以分配的最大内存。然后进行深度优先遍历，找到合适的节点，将其longest标记为0（即分离适配的块出来），并转换为内存块索引offset返回
在返回之前仍需要回溯父节点，将它们的longest进行折扣计算，取左右子树的最大值

## 内存释放：
输入参数为分配器指针及内存地址索引。之后从最底层节点向上寻找longest为0的节点，即当初分配块所适配的大小和位置，将longest恢复到原来满状态的值。继续向上回溯，检查是否存在可以合并的块，依据左右子树的longest值相加是否等于原空闲状态下longest的大小，检测能否进行合并

## TODO：
- [_] test function
- [_] error handling