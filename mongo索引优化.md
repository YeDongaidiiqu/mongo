##mongo索引

###索引说明
mongo 中索引和数据是存放在一起的,索引的结构是B tree

###索引类型
1. 单键索引  
给单个field添加索引  
db.collectionName.createIndex(fieldName:1)  
给二级字段加索引  
db.collectionName.createIndex(fieldName.twoFieldName:1)     
> ensureIndex 是 createIndex的别名  

 


2. 复合索引
3. 


###优化案例
