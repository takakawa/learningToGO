# GO局部变量不一定是在栈上
看函数
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