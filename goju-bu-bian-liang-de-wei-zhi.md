# 局部变量不一定在栈上
分析代码
```go
func closeurerw(a int) (func(x int) int){
    local:=1
    local2:=1
     internal:=func(b int) int {
        local ++;
        local2 ++;
        return a+b+local+local2
    }
    return internal
}
```

# 汇编
```
"".closeurerw STEXT size=262 args=0x10 locals=0x38
	0x0000 00000 (main.go:23)	TEXT	"".closeurerw(SB), $56-16
	0x0000 00000 (main.go:23)	MOVQ	(TLS), CX
	0x0009 00009 (main.go:23)	CMPQ	SP, 16(CX)
	0x000d 00013 (main.go:23)	JLS	252
	0x0013 00019 (main.go:23)	SUBQ	$56, SP
	0x0017 00023 (main.go:23)	MOVQ	BP, 48(SP)
	0x001c 00028 (main.go:23)	LEAQ	48(SP), BP
	0x0021 00033 (main.go:23)	FUNCDATA	$0, gclocals·d43560e00d694c31b947b5a80212ab3d(SB)
	0x0021 00033 (main.go:23)	FUNCDATA	$1, gclocals·7d68339d7f8fcdb4db483363451a3412(SB)
	0x0021 00033 (main.go:23)	LEAQ	type.int(SB), AX        # 构造runtime.newobject参数，其参数为一个_type类型的指针
	0x0028 00040 (main.go:24)	MOVQ	AX, (SP)                # 将构造参数入栈
	0x002c 00044 (main.go:24)	PCDATA	$0, $0
	0x002c 00044 (main.go:24)	CALL	runtime.newobject(SB)   # 调用 newobject分配内存
	0x0031 00049 (main.go:24)	MOVQ	8(SP), AX               # 将出参入栈，函数只有一个参数，故SP+8指向出参 
	0x0036 00054 (main.go:24)	MOVQ	AX, "".&local+40(SP)    # 将分配的int的地址放到栈上(SP+40=BP-8)
	0x003b 00059 (main.go:24)	MOVQ	$1, (AX)                # 对申请的内存赋初值1
	0x0042 00066 (main.go:24)	LEAQ	type.int(SB), CX        # 申请local2，过程同上
	0x0049 00073 (main.go:25)	MOVQ	CX, (SP)
	0x004d 00077 (main.go:25)	PCDATA	$0, $1
	0x004d 00077 (main.go:25)	CALL	runtime.newobject(SB)
	0x0052 00082 (main.go:25)	MOVQ	8(SP), AX
	0x0057 00087 (main.go:25)	MOVQ	AX, "".&local2+32(SP)
	0x005c 00092 (main.go:25)	MOVQ	$1, (AX)
	0x0063 00099 (main.go:25)	LEAQ	type.struct { F uintptr; "".local *int; "".local2 *int; "".a int }(SB), CX # 构造闭包函数的上下文，这里自动定义了一个结构体
	0x006a 00106 (main.go:26)	MOVQ	CX, (SP)
	0x006e 00110 (main.go:26)	PCDATA	$0, $2
	0x006e 00110 (main.go:26)	CALL	runtime.newobject(SB)
	0x0073 00115 (main.go:26)	MOVQ	8(SP), AX
	0x0078 00120 (main.go:26)	LEAQ	"".closeurerw.func1(SB), CX
	0x007f 00127 (main.go:26)	MOVQ	CX, (AX)
	0x0082 00130 (main.go:26)	TESTB	AL, (AX)
	0x0084 00132 (main.go:26)	MOVL	runtime.writeBarrier(SB), CX    # 内存屏障
	0x008a 00138 (main.go:26)	LEAQ	8(AX), DX
	0x008e 00142 (main.go:26)	LEAQ	16(AX), BX
	0x0092 00146 (main.go:26)	TESTL	CX, CX
	0x0094 00148 (main.go:26)	JNE	192
	0x0096 00150 (main.go:26)	MOVQ	"".&local+40(SP), CX         # 获取local地址
	0x009b 00155 (main.go:26)	MOVQ	CX, 8(AX)                    # 将local的地址放入struct中的local位置
	0x009f 00159 (main.go:26)	MOVQ	"".&local2+32(SP), CX        # 获取local2地址
	0x00a4 00164 (main.go:26)	MOVQ	CX, 16(AX)                   # 将local2的地址放入struct中的local2位置
	0x00a8 00168 (main.go:26)	MOVQ	"".a+64(SP), CX              # 获取入参a的内容
	0x00ad 00173 (main.go:26)	MOVQ	CX, 24(AX)                   # 放入struct中a的位置
	0x00b1 00177 (main.go:31)	MOVQ	AX, "".~r1+72(SP)            # 将struct的地址放到返回参数位置
	0x00b6 00182 (main.go:31)	MOVQ	48(SP), BP                   # 恢复BP
	
	0x00bb 00187 (main.go:31)	ADDQ	$56, SP			# 回退栈
	0x00bf 00191 (main.go:31)	RET
	0x00c0 00192 (main.go:31)	MOVQ	AX, ""..autotmp_9+16(SP)
	0x00c5 00197 (main.go:31)	MOVQ	BX, ""..autotmp_10+24(SP)
	0x00ca 00202 (main.go:26)	MOVQ	DX, (SP)
	0x00ce 00206 (main.go:26)	MOVQ	"".&local+40(SP), AX
	0x00d3 00211 (main.go:26)	MOVQ	AX, 8(SP)
	0x00d8 00216 (main.go:26)	PCDATA	$0, $3
	0x00d8 00216 (main.go:26)	CALL	runtime.writebarrierptr(SB)
	0x00dd 00221 (main.go:26)	MOVQ	""..autotmp_10+24(SP), AX
	0x00e2 00226 (main.go:26)	MOVQ	AX, (SP)
	0x00e6 00230 (main.go:26)	MOVQ	"".&local2+32(SP), AX
	0x00eb 00235 (main.go:26)	MOVQ	AX, 8(SP)
	0x00f0 00240 (main.go:26)	PCDATA	$0, $4
	0x00f0 00240 (main.go:26)	CALL	runtime.writebarrierptr(SB)
	0x00f5 00245 (main.go:26)	MOVQ	""..autotmp_9+16(SP), AX
	0x00fa 00250 (main.go:26)	JMP	168
	0x00fc 00252 (main.go:26)	NOP
	0x00fc 00252 (main.go:23)	PCDATA	$0, $-1
	0x00fc 00252 (main.go:23)	CALL	runtime.morestack_noctxt(SB)
	0x0101 00257 (main.go:23)	JMP	0
	0x0000 65 48 8b 0c 25 00 00 00 00 48 3b 61 10 0f 86 e9  eH..%....H;a....
	0x0010 00 00 00 48 83 ec 38 48 89 6c 24 30 48 8d 6c 24  ...H..8H.l$0H.l$
	0x0020 30 48 8d 05 00 00 00 00 48 89 04 24 e8 00 00 00  0H......H..$....
	0x0030 00 48 8b 44 24 08 48 89 44 24 28 48 c7 00 01 00  .H.D$.H.D$(H....
	0x0040 00 00 48 8d 0d 00 00 00 00 48 89 0c 24 e8 00 00  ..H......H..$...
	0x0050 00 00 48 8b 44 24 08 48 89 44 24 20 48 c7 00 01  ..H.D$.H.D$ H...
	0x0060 00 00 00 48 8d 0d 00 00 00 00 48 89 0c 24 e8 00  ...H......H..$..
	0x0070 00 00 00 48 8b 44 24 08 48 8d 0d 00 00 00 00 48  ...H.D$.H......H
	0x0080 89 08 84 00 8b 0d 00 00 00 00 48 8d 50 08 48 8d  ..........H.P.H.
	0x0090 58 10 85 c9 75 2a 48 8b 4c 24 28 48 89 48 08 48  X...u*H.L$(H.H.H
	0x00a0 8b 4c 24 20 48 89 48 10 48 8b 4c 24 40 48 89 48  .L$ H.H.H.L$@H.H
	0x00b0 18 48 89 44 24 48 48 8b 6c 24 30 48 83 c4 38 c3  .H.D$HH.l$0H..8.
	0x00c0 48 89 44 24 10 48 89 5c 24 18 48 89 14 24 48 8b  H.D$.H.\$.H..$H.
	0x00d0 44 24 28 48 89 44 24 08 e8 00 00 00 00 48 8b 44  D$(H.D$......H.D
	0x00e0 24 18 48 89 04 24 48 8b 44 24 20 48 89 44 24 08  $.H..$H.D$ H.D$.
	0x00f0 e8 00 00 00 00 48 8b 44 24 10 eb ac e8 00 00 00  .....H.D$.......
	0x0100 00 e9 fa fe ff ff                                ......
	rel 5+4 t=16 TLS+0
	rel 36+4 t=15 type.int+0
	rel 45+4 t=8 runtime.newobject+0
	rel 69+4 t=15 type.int+0
	rel 78+4 t=8 runtime.newobject+0
	rel 102+4 t=15 type.struct { F uintptr; "".local *int; "".local2 *int; "".a int }+0
	rel 111+4 t=8 runtime.newobject+0
	rel 123+4 t=15 "".closeurerw.func1+0
	rel 134+4 t=15 runtime.writeBarrier+0
	rel 217+4 t=8 runtime.writebarrierptr+0
	rel 241+4 t=8 runtime.writebarrierptr+0
	rel 253+4 t=8 runtime.morestack_noctxt+0

```