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

## 完整代码

```Python
class BinaryTree(object):
    """ 创建一个二叉树实例
    Args:
    Attributes:
        key: 节点值
        leftChild: 指向新的左子树
        rightChild: 指向新的右子树
    Returns:
    Raises:
    IOError:
    """

    def __init__(self, rootObj) -> None:
        self.key = rootObj
        self.leftChild = None
        self.rightChild = None

    def getLeftChild(self):
        """
        返回当前节点的左子节点所对应的二叉树
        Args:
        Return: 当前节点的左子树
        """
        return self.leftChild

    def getRightChild(self):
        """
        返回当前节点的右子节点所对应的二叉树
        Args:
        Return: 当前节点的右子树
        """
        return self.rightChild

    def setRootVal(self, obj):
        """
        在当前节点中存储参数 val 中的对象
        Args:
            obj: 希望存入当前节点的值
        Return:
        """
        self.key = obj

    def getRootVal(self):
        """
        返回当前节点存储的对象
        """
        return self.key

    def insertLeft(self, newNode):
        """
        新建一棵二叉树，并将其作为当前节点的左子节点
        Args:
            newNode: 新二叉树的值
        Return:
        """
        if self.leftChild == None:
            self.leftChild = BinaryTree(newNode)
        else:
            t = BinaryTree(newNode)
            t.left = self.leftChild
            self.leftChild = t

    def insertRight(self, newNode):
        """
        新建一棵二叉树，并将其作为当前节点的右子节点
        Args:
            newNode: 新二叉树的值
        Return:
        """
        if self.rightChild == None:
            self.rightChild = BinaryTree(newNode)
        else:
            t = BinaryTree(newNode)
            t.right = self.rightChild
            self.rightChild = t
```
