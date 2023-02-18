# 树

- ```BinaryTree()``` 创建一个二叉树实例

```Python
class BinaryTree:

    def __init__(self, rootObj) -> None:
        self.key = rootObj
        self.leftChild = None
        self.rightChild = None
```

- ```getLeftChild()``` 返回当前节点的左子节点所对应的二叉树

```Python
    def getLeftChild(self):
        return self.leftChild
```

- ```getRightChild()``` 返回当前节点的右子节点所对应的二叉树

```Python
    def getRightChild(self):
        return self.rightChild
```

- ```setRootVal(val)``` 在当前节点中存储参数 val 中的对象

```Python
    def setRootVal(self, obj):
        self.key = obj
```

- ```getRootVal()``` 返回当前节点存储的对象

```Python
    def getRootVal(self):
        return self.key
```

- ```insertLeft(val)``` 新建一棵二叉树，并将其作为当前节点的左子节点

```Python
    def insertLeft(self, newNode):
        if self.leftChild == None:
            self.leftChild = BinaryTree(newNode)
        else:
            t = BinaryTree(newNode)
            t.left = self.leftChild
            self.leftChild = t
```

- ```insertRight(val)``` 新建一棵二叉树，并将其作为当前节点的右子节点

```Python
    def insertRight(self, newNode):
        if self.rightChild == None:
            self.rightChild = BinaryTree(newNode)
        else:
            t = BinaryTree(newNode)
            t.right = self.rightChild
            self.rightChild = t
```
