# 栈

## 基与列表实现栈

- ```Stack()``` 创建一个空栈。它不需要参数，且会返回一个空栈。

```Python
class Stack(object):
    __slots__ = '__items'

    def __init__(self):
        self.__items = list()
```

- ```push(item)``` 将一个元素添加到栈的顶端。它需要一个参数 item ，且无返回值。

```Python
    def push(self, item):
        self.__items.append(item)
```

- ```pop()``` 将栈顶端的元移除。它不需要参数，但会返回顶端的元素，并且修改栈的内容。

```Python
    def pop(self):
        if self.isEmpty():
            return
        return self.__items.pop()
```

- ```peek()``` 返回栈顶端得元素。但是不移除该元素。它不需要参数，也不会修改栈的内容。

```Python
    def peek(self):
        if self.isEmpty():
            return
        return self.__items[len(self.__items) - 1]
```

- ```isEmpty()``` 检查栈是否为空。它不需要参数，且会返回一个布尔值。

```Python
    def isEmpty(self):
        return self.__items is None
```

- ```size()``` 返回栈中元素的数目。它不需要参数，且会返回一个整数。

```Python
    def size(self):
        if self.isEmpty():
            return 0
        return len(self.__items)
```

### 完整代码

```Python
class Stack(object):
    __slots__ = '__items'

    def __init__(self):
        self.__items = list()

    def push(self, item):
        self.__items.append(item)

    def pop(self):
        if self.isEmpty():
            return
        return self.__items.pop()

    def peek(self):
        if self.isEmpty():
            return
        return self.__items[len(self.__items) - 1]

    def isEmpty(self):
        return self.__items == []

    def size(self):
        if self.isEmpty():
            return 0
        return len(self.__items)
```

## 基与 Node节点实现栈

### Node 类

```Python
class Node(object):
    __slots__ = ('__data', '__next')

    def __init__(self, initdata=None):
        self.__data = initdata
        self.__next = None

    def getData(self):
        return self.__data

    def getNext(self):
        return self.__next

    def setData(self, newData):
        self.__data = newData

    def setNext(self, newNext):
        self.__next = newNext
```

### Stack 类

```Python
class Stack(object):
    __slots__ = ('__head', '__length')

    def __init__(self):
        self.__head = None
        self.__length = 0

    def push(self, item):
        temp = Node(item)
        temp.setNext(self.__head)
        self.__head = temp
        self.__length += 1

    def pop(self):
        if self.__head is None:
            return
        item = self.__head.getData()
        self.__head = self.__head.getNext()
        self.__length -= 1
        return item

    def peek(self):
        if self.__head is None:
            return
        return self.__head.getData()

    def isEmpty(self):
        return self.__head is None

    @property
    def size(self):
        return self.__length
```
