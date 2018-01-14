# GO汇编和最终汇编对比
  
go语言通过go tool compile -S xx.go >> xx.S生成的为plan9的汇编语法

* GO函数栈分析
  先看一个函数的栈
  ![](/assets/屏幕快照 2018-01-13 下午12.28.50.png)
  go的函数栈和C语言的基本相似，但是有些变化

流程


注意:

* GO多值返回使用栈来传输
* GO的栈在一开始就分配好大写，对局部变量和入参，返回函数的使用都通过和SP的偏移来计算


{% mermaid %}
graph TD;
  A-->B;
  A-->C;
  B-->D;
  C-->D;
{% endmermaid %}


