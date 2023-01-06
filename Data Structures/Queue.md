# 队列

## 基与列表实现一个队列

- ```Queue()``` 创建一个空队列。它不需要参数，且会返回一个空队列。

```Python
class Queue(object):
    __slots__ = '__items'

    def __init__(self):
        self.__items = list()
```

- ```enqueue(item)``` 在队列尾部添加一个元素。它需要一个元素作为参数，不返回任何值。

```Python
    def enqueue(self, item):
        self.__items.insert(0, item)
```

- ```dequeue()``` 从队列的头移除一个元素。它不需要参数，且会返回一个元素，并修改队列的内容。

```Python
    def dequeue(self):
        if self.isEmpty():
            return
        return self.__items.pop()
```

- ```isEmpty()``` 检查队列是否为空。它不需要参数，且会返回一个布尔值。

```Python
    def isEmpty(self):
        return self.__items == []
```

- ```size()``` 返回队列中的元素数目。它不需要参数，且会返回一个整数。

```Python
    def size(self):
        if self.isEmpty():
            return 0
        return len(self.__items)
```

### 完整代码

```Python
class Queue(object):
    __slots__ = '__items'

    def __init__(self):
        self.__items = list()

    def enqueue(self, item):
        self.__items.insert(0, item)

    def dequeue(self):
        if self.isEmpty():
            return
        return self.__items.pop()

    def isEmpty(self):
        return self.__items == []

    def size(self):
        if self.isEmpty():
            return 0
        return len(self.__items)
```

## 基与 Node节点实现队列

### Node类

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

### Queue 类

```Python
class Queue(object):
    __slots__ = ('__head', '__length')

    def __init__(self):
        self.__head = None
        self.__length = 0

    def enqueue(self, item):
        temp = Node(item)
        temp.setNext(self.__head)
        self.__head = temp
        self.__length += 1

    def dequeue(self):
        if self.__head is None:
            return
        current = self.__head
        previous = None
        while current.getNext():
            previous = current
            current = current.getNext()
        item = current.getData()
        previous.setNext(current.getNext())
        self.__length -= 1
        return item

    def isEmpty(self):
        return self.__head is None

    @property
    def size(self):
        return self.__length
```
