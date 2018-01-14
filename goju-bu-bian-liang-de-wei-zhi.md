# 局部变量不一定在栈上
```
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