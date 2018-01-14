# GO汇编和最终汇编对比

go语言通过go tool compile -S xx.go >> xx.S生成的为plan9的汇编语法

源函数：

```go
func closeure(a int) (func(x int) int){

    internal:=func(b int) int {
      return a+b
    }
    
    return internal
}
```

plan9汇编：

```
"".closeure STEXT size=91 args=0x10 locals=0x18
	0x0000 00000 (main.go:23)	TEXT	"".closeure(SB), $24-16
	0x0000 00000 (main.go:23)	MOVQ	(TLS), CX
	0x0009 00009 (main.go:23)	CMPQ	SP, 16(CX)
	0x000d 00013 (main.go:23)	JLS	84
	0x000f 00015 (main.go:23)	SUBQ	$24, SP
	0x0013 00019 (main.go:23)	MOVQ	BP, 16(SP)
	0x0018 00024 (main.go:23)	LEAQ	16(SP), BP
	0x001d 00029 (main.go:23)	FUNCDATA	$0, gclocals·f207267fbf96a0178e8758c6e3e0ce28(SB)
	0x001d 00029 (main.go:23)	FUNCDATA	$1, gclocals·33cdeccccebe80329f1fdbee7f5874cb(SB)
	0x001d 00029 (main.go:23)	LEAQ	type.struct { F uintptr; "".a int }(SB), AX
	0x0024 00036 (main.go:24)	MOVQ	AX, (SP)
	0x0028 00040 (main.go:24)	PCDATA	$0, $0
	0x0028 00040 (main.go:24)	CALL	runtime.newobject(SB)
	0x002d 00045 (main.go:24)	MOVQ	8(SP), AX
	0x0032 00050 (main.go:24)	LEAQ	"".closeure.func1(SB), CX
	0x0039 00057 (main.go:24)	MOVQ	CX, (AX)
	0x003c 00060 (main.go:23)	MOVQ	"".a+32(SP), CX
	0x0041 00065 (main.go:24)	MOVQ	CX, 8(AX)
	0x0045 00069 (main.go:27)	MOVQ	AX, "".~r1+40(SP)
	0x004a 00074 (main.go:27)	MOVQ	16(SP), BP
	0x004f 00079 (main.go:27)	ADDQ	$24, SP
	0x0053 00083 (main.go:27)	RET
	0x0054 00084 (main.go:27)	NOP
	0x0054 00084 (main.go:23)	PCDATA	$0, $-1
	0x0054 00084 (main.go:23)	CALL	runtime.morestack_noctxt(SB)
	0x0059 00089 (main.go:23)	JMP	0
	0x0000 65 48 8b 0c 25 00 00 00 00 48 3b 61 10 76 45 48  eH..%....H;a.vEH
	0x0010 83 ec 18 48 89 6c 24 10 48 8d 6c 24 10 48 8d 05  ...H.l$.H.l$.H..
	0x0020 00 00 00 00 48 89 04 24 e8 00 00 00 00 48 8b 44  ....H..$.....H.D
	0x0030 24 08 48 8d 0d 00 00 00 00 48 89 08 48 8b 4c 24  $.H......H..H.L$
	0x0040 20 48 89 48 08 48 89 44 24 28 48 8b 6c 24 10 48   H.H.H.D$(H.l$.H
	0x0050 83 c4 18 c3 e8 00 00 00 00 eb a5                 ...........
	rel 5+4 t=16 TLS+0
	rel 32+4 t=15 type.struct { F uintptr; "".a int }+0
	rel 41+4 t=8 runtime.newobject+0
	rel 53+4 t=15 "".closeure.func1+0
	rel 85+4 t=8 runtime.morestack_noctxt+0
```


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


