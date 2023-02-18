# 双端队列

- ```Deque``` 创建一个空的双端队列

```Python
class Deque():
    def __init__(self) -> None:
        self.items = []
```

- ```addFront(item)``` 将一个元素添加到双端队列的前端。它接受一个元素作为参数，没有
返回值

```Python
def addFront(self, item) -> None:
        self.items.append(item)
```

- ```addRear(item)``` 将一个元素添加到双端队列的后端。它接受一个元素作为参数，没有返
回值。

```Python
def addRear(self, item) -> None:
        self.items.insert(0,item)
```

- ```removeFront()```从双端队列的前端移除一个元素。它不需要参数，且会返回一个元素，
并修改双端队列的内容。

```Python
def removeFront(self):
        return self.items.pop()
```

- ```removeRear()``` 从双端队列的后端移除一个元素。它不需要参数，且会返回一个元素，
并修改双端队列的内容。

```Python
def removeRear(self):
        return self.items.pop(0)
```

- ```isEmpty()``` 检查双端队列是否为空。它不需要参数，且会返回一个布尔值。

```Python
def isEmpty(self) -> bool:
        return self.items == []
```

- ```size()``` 返回双端队列中元素的数目。它不需要参数，且会返回一个整数。

```Python
def size(self):
        return len(self.items)
```

```Python
class Deque():
    def __init__(self) -> None:
        self.items = []
    
    def addFront(self, item) -> None:
        self.items.append(item)

    def addRear(self, item) -> None:
        self.items.insert(0,item)

    def removeFront(self):
        return self.items.pop()

    def removeRear(self):
        return self.items.pop(0)

    def isEmpty(self) -> bool:
        return self.items == []

    def size(self):
        return len(self.items)
```
