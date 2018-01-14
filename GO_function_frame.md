# GO汇编说明

go语言通过以下命令可以将go源文件xx.go转成xx.S，以查看go语言的汇编

```go tool compile -S xx.go >> xx.S```

生成的为plan9的汇编语法,plan9汇编最终会翻译为对应目标机器汇编

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

```asm
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

此汇编与大数我们常见的汇编语法基本相同，但是有很多奇怪的表示

>  TEXT    "".closeure(SB), $24-16


目标机器汇编（MacPro)：

```asm
Dump of assembler code for function main.closeure:
   0x0000000001093720 <+0>:	65 48 8b 0c 25 a0 08 00 00	mov    %gs:0x8a0,%rcx
   0x0000000001093729 <+9>:	48 3b 61 10	cmp    0x10(%rcx),%rsp
   0x000000000109372d <+13>:	76 45	jbe    0x1093774 <main.closeure+84>
   0x000000000109372f <+15>:	48 83 ec 18	sub    $0x18,%rsp
   0x0000000001093733 <+19>:	48 89 6c 24 10	mov    %rbp,0x10(%rsp)
   0x0000000001093738 <+24>:	48 8d 6c 24 10	lea    0x10(%rsp),%rbp
   0x000000000109373d <+29>:	48 8d 05 5c a4 01 00	lea    0x1a45c(%rip),%rax        # 0x10adba0 <type.*+106912>
=> 0x0000000001093744 <+36>:	48 89 04 24	mov    %rax,(%rsp)
   0x0000000001093748 <+40>:	e8 b3 b7 f7 ff	callq  0x100ef00 <runtime.newobject>
   0x000000000109374d <+45>:	48 8b 44 24 08	mov    0x8(%rsp),%rax
   0x0000000001093752 <+50>:	48 8d 0d 27 02 00 00	lea    0x227(%rip),%rcx        # 0x1093980 <main.closeure.func1>
   0x0000000001093759 <+57>:	48 89 08	mov    %rcx,(%rax)
   0x000000000109375c <+60>:	48 8b 4c 24 20	mov    0x20(%rsp),%rcx
   0x0000000001093761 <+65>:	48 89 48 08	mov    %rcx,0x8(%rax)
   0x0000000001093765 <+69>:	48 89 44 24 28	mov    %rax,0x28(%rsp)
   0x000000000109376a <+74>:	48 8b 6c 24 10	mov    0x10(%rsp),%rbp
   0x000000000109376f <+79>:	48 83 c4 18	add    $0x18,%rsp
   0x0000000001093773 <+83>:	c3	retq
   0x0000000001093774 <+84>:	e8 87 ab fb ff	callq  0x104e300 <runtime.morestack_noctxt>
   0x0000000001093779 <+89>:	eb a5	jmp    0x1093720 <main.closeure>
End of assembler dump.
```
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


