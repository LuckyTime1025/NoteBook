# 图

## 邻接表

```Python
class Vertex(object):

    def __init__(self, key) -> None:
        self.id = key
        self.connectedTo = {}

    def __str__(self) -> str:
        return str(self.id) + 'connectedTo: ' + str(
            [x.id for x in self.connectedTo])

    def getConnections(self) -> list:
        return self.connectedTo.keys()

    def getId(self):
        return self.id

    def getWeight(self, nbr):
        return self.connectedTo[nbr]


class Graph(object):

    def __init__(self) -> None:
        self.vertList = {}
        self.numVertices = 0

    def __contains__(self, n):
        return n in self.vertList

    def __iter__(self):
        return iter(self.vertList.values())

    def addVertex(self, key):
        self.numVertices = self.numVertices + 1
        newVertex = Vertex(key)
        self.vertList[key] = newVertex
        return newVertex

    def getVertex(self, n):
        if n in self.vertList:
            return self.vertList[n]
        else:
            return None

    def addEdge(self, f, t, cost=0):
        if f not in self.vertList:
            nv = self.addVertex(f)
        if t not in self.vertList:
            nv = self.addVertex(t)
        self.vertList[f].addNeighbor(self.vertList[t], cost)

    def getVertices(self):
        return self.vertList.keys()
```
