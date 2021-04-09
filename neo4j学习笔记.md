## neo4j学习笔记

1. 节点相关

   ```CQL
   //注意：
   //1. 这里的node-name(类似于一个局部变量的形式)，也就是说如果不指定标签名进行match根本找不到节点,删除需要先用match找到这个顶点
   //2. 所有的<>符号表示的是可替换的内容，如：
   //3. create (p1:Provider)
   create (<node-name>:<label1-name>) 创建/绑定节点和标签
   create (<node-name>:<label2-name>:<label3-name>) 绑定多个标签到顶点
   match (<node-name>:<label-name>) return <node-name>.<attr-name> 查找/匹配一个节点，返回节点的属性
   create (<node1-name>:<label1-name>)-[(<relationship-name>:<relationship-label-name>)]->(<node2-name>:<label2-name>) 
   delete <node1-name>,<node2-name>,<relationship-name> 删除节点或者关系
   remove <node-name>.<property1-name>,<node-name>.<property2-name> 删除节点属性 
   
   ```

   

