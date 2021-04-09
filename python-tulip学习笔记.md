# python-tulip学习笔记

- json导入时最简图结构


```json
{
    "graph":{"nodesNumber":2,"edgesNumber":1,"edges":[[0,1]]
}

```

- 现在存在的问题就是导入图时id不对称。
- 在tulip中，往往得到一个先得到一个属性的value，然后在这个property的基础之上添加getNodeValue的方法获得值。