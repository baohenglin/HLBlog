iOS之Block探究篇

## block的本质

block本质上是一个OC对象,因为它内部有一个isa指针。更确切地说，block是封装了函数调用以及函数调用环境的OC的对象。

## block常见的面试题
(1)block的本质是什么?底层原理是怎样的?

block本质上是一个OC对象,因为它内部有一个isa指针。更确切地说，block是封装了函数调用以及函数调用环境的OC的对象。

(2)__block的作用是什么?使用时需要注意什么?

(3)block的属性修饰词为什么是copy？使用block有哪些注意事项？

(4)block在修改NSMutableArray时，需不需要加__block？