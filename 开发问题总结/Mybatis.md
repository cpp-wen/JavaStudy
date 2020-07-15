默认方法 

###### Date类在开发时候如何和数据库之间进行一个映射 todo

updateByPrimaryKeySelective和 updateByPrimaryKey区别 主要是更新字段 第一个非空的时候会跳过更新

对于mysql数据库中对于boolean的定义一般都使用 tinyInt(1)数据库会自动实现true和false的转换 默认1为true  0为false|