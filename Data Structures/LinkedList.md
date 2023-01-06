# 链表

## 无序链表

### Node 类  

> 节点 Node 类是构建链表的基本数据结构。每一个节点对象都必须持有至少两份信息。首先，节点必须包含列表元素，我们称之为节点的数据变量。其次，节点必须保存指向下一个节点的引用。

- ```getData``` : 获取当前节点的值
- ```getNext``` : 获取当前节点指向的下一个节点
- ```setData``` : 设置当前节点的值
- ```setNext``` : 设置当前节点指向的下一个节点

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

### UnorderedList 类

> 无序列表（unordered list）是基于节点集合来构建的，每一个节点都通过显式的引用指向下一个节点。只要知道第一个节点的位置（第一个节点包含第一个元素）其后的每一个元素都能通过下一个引用找到。因此 UnorderedList 类必须包含指向第一个节点的引用。

```Python
class UnorderedList(object):
    __slots__ = ('__head', '__length')

    def __init__(self):
        self.__head = None
        self.__length = 0

    def __contains__(self, item):
        return self.search(item)

    def __str__(self):
        return f'{[i for i in self.__iter__()]}'

    def __iter__(self):
        current = self.__head
        while current:
            yield current.getData()
            current = current.getNext()
```

一个无序列表通常有以下方法

- ```length()``` : 返回列表的长度

```Python
    @property
    def length(self):
        return self.__length
```

- ```isEmpty()``` : 返回列表是否为空

```Python
    def isEmpty(self):
        return self.__head is None
```

- ```add(item)``` : 在列表头部添加元素

```Python
    def add(self, item):
        temp = Node(item)
        temp.setNext(self.__head)
        self.__head = temp
        self.__length += 1
```

- ```append(item)``` : 在列表尾部添加元素

```Python
    def append(self, item):
        if self.__head is None:
            self.add(item)
            return
        else:
            current = self.__head
            while current.getNext():
                current = current.getNext()
            temp = Node(item)
            current.setNext(temp)
            self.__length += 1
```

- ```remove(item)``` : 移除给定的元素

```Python
    def remove(self, item):
        current = self.__head
        previous = None

        while current:
            if current.getData() == item:
                if current == self.__head:
                    self.__head = current.getNext()
                    previous = current
                    current = current.getNext()
                else:
                    previous.setNext(current.getNext())
                    previous = current
                    current = current.getNext()
            else:
                previous = current
                current = current.getNext()
        return False
```

- ```pop(pos)``` : 一处给定位置的元素，并返回其值

```Python
    def pop(self, pos=None):
        if 0 > pos > self.__length:
            return
        current = self.__head
        previous = None
        if self.__head is None:
            return
        else:
            if pos:
                count = 0
                while current:
                    if count == pos:
                        previous.setNext(current.getNext())
                        return current.getData()
                    else:
                        count += 1
                        previous = current
                        current = current.getNext()
            else:
                while current.getNext():
                    previous = current
                    current = current.getNext()

                if previous is None:
                    self.__head = current.getNext()
                else:
                    previous.setNext(None)
```

- ```replace(self, old_item, new_item)``` : 替换链表中的元素

```Python
    def replace(self, old_item, new_item):
        if self.__head is None:
            return False
        current = self.__head
        while current:
            if current.getData() == old_item:
                current.setData(new_item)
            else:
                current = current.getNext()
        return True
```

- ```search(item)``` : 查找给定元素是否存在

```Python
    def search(self, item):
        current = self.__head
        found = False
        while current and not found:
            if current.getData() == item:
                found = True
            else:
                current = current.getNext()
        return found
```

- ```index(item)``` : 返回给定元素的位置

```Python
    def index(self, item):
        current = self.__head
        count = 0
        while current:
            if current.getData() == item:
                return count
            else:
                count += 1
                current = current.getNext()
        else:
            return None
```

### 完整代码

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


class UnorderedList(object):
    __slots__ = ('__head', '__length')

    def __init__(self):
        self.__head = None
        self.__length = 0

    def __contains__(self, item):
        return self.search(item)

    def __str__(self):
        return f'{[i for i in self.__iter__()]}'

    def __iter__(self):
        current = self.__head
        while current:
            yield current.getData()
            current = current.getNext()

    @property
    def length(self):
        return self.__length

    def isEmpty(self):
        return self.__head is None

    def add(self, item):
        temp = Node(item)
        temp.setNext(self.__head)
        self.__head = temp
        self.__length += 1

    def append(self, item):
        if self.__head is None:
            self.add(item)
            return
        else:
            current = self.__head
            while current.getNext():
                current = current.getNext()
            temp = Node(item)
            current.setNext(temp)
            self.__length += 1

    def remove(self, item):
        current = self.__head
        previous = None

        while current:
            if current.getData() == item:
                if current == self.__head:
                    self.__head = current.getNext()
                    previous = current
                    current = current.getNext()
                else:
                    previous.setNext(current.getNext())
                    previous = current
                    current = current.getNext()
            else:
                previous = current
                current = current.getNext()
        return False

    def pop(self, pos=None):
        if 0 > pos > self.__length:
            return
        current = self.__head
        previous = None
        if self.__head is None:
            return
        else:
            if pos:
                count = 0
                while current:
                    if count == pos:
                        previous.setNext(current.getNext())
                        return current.getData()
                    else:
                        count += 1
                        previous = current
                        current = current.getNext()
            else:
                while current.getNext():
                    previous = current
                    current = current.getNext()

                if previous is None:
                    self.__head = current.getNext()
                else:
                    previous.setNext(None)

    def replace(self, old_item, new_item):
        if self.__head is None:
            return False
        current = self.__head
        while current:
            if current.getData() == old_item:
                current.setData(new_item)
            else:
                current = current.getNext()
        return True

    def search(self, item):
        current = self.__head
        found = False
        while current and not found:
            if current.getData() == item:
                found = True
            else:
                current = current.getNext()
        return found

    def index(self, item):
        current = self.__head
        count = 0
        while current:
            if current.getData() == item:
                return count
            else:
                count += 1
                current = current.getNext()
        else:
            return None

```

## 有序链表

> 在有序列表中，元素的相对位置取决与它们的基本特征。

### OrderedList 类

```Python
class OrderedList(object):
    __slots__ = ('__head', '__length')

    def __init__(self):
        self.__head = None
        self.__length = 0

    def __contains__(self, item):
        return self.search(item)

    def __str__(self):
        return f'{[i for i in self.__iter__()]}'

    def __iter__(self):
        current = self.__head
        while current:
            yield current.getData()
            current = current.getNext()
```

因为 isEmpty 和 length 仅与列表中的节点数有关，而与实际的元素值无关，所以这两个方法在有序列表中的实现与在无序列表中一样。同理，由于仍然需要找到目标元素并且通过改链移除节点，因此 remove 方法的实现也一样。

- ```add(item)``` 必须要确定新元素的位置

```Python
    def add(self, item):
        current = self.__head
        previous = None
        temp = Node(item)
        if self.__head is None:
            temp.setNext(self.__head)
            self.__head = temp
            self.__length += 1
        else:
            while current:
                if current.getData() > item:
                    temp.setNext(current)
                    previous.setNext(temp)
                    self.__length += 1
                    return
                previous = current
                current = current.getNext()
            else:
                previous.setNext(temp)
                self.__length += 1
```

- ```search(item)``` 可以利用元素有序排列这一特性尽早终止搜索。

```Python
    def search(self, item):
        current = self.__head
        while current:
            if current.getData() > item:
                return False
            else:
                if current.getData() == item:
                    return True
            current = current.getNext()
        return False
```
