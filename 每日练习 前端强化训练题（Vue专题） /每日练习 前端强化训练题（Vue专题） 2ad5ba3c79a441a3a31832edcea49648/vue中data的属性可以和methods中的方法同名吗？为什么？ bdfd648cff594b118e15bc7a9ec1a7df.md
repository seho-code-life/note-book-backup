# vue中data的属性可以和methods中的方法同名吗？为什么？

# vue中data的属性可以和methods中的方法同名吗？为什么？

可以同名，但是vue会执行data，不会执行methods，并且会报错：说这个名称已经被data使用过了