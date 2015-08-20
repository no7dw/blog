## 固定集合 ##

将已有的集合转化为固定集合
    db.runCommand({convertToCapped:"test",size:10000});
    
    //check
    db.test.state()
    //isCap:true
    
转的时候size 是collection的大小，
另外创建的时候可以制定，最大的documents size （max 属性）

    db.createCollection("collect",{capped:true, size:10000, max:20});

坑：创建后，手动create >20 条纪录时，会出现先进先出的队列效果。

 - 但在线上发现，超过了大小，再也新增不了。
 - 尝试将原的size增加，看看能否插新数据，还是不行。
 - 尝试 undo  capped , stackoverflow 后发现只有rename ,and recreate data 的方法。由于创建大量数据会锁库很严重。于是紧紧rename 了这个数据库（没有旧数据关系不大，所以这么做）


