```assembly
;imul符号数乘法
;ax = al*src
;dx:ax = ax*src 
;edx:eax = eax*src
mov al,0ffh
mov bl,1
imul bl; ax = 0ffffh = -1

imul eax,ebx,1234h;第三个参数只能是常数，不能是寄存器，中间一个既可以是寄存器也可以是变量
imul eax,dword ptr [ebx+2],1234h
;eax = ebx*1234h
imul eax,ebx;第二个参数还可以是变量
;eax = eax*ebx
;warning:结果一定是32位，如果溢出的话of=1

;除法指令div idiv
;div op
;ax / 8位 = al(商) ah(余数)
;dx:ax / 16位 = ax(商) dx(余数)
;edx:eax / 32位 = eax edx
mov ax,1234h
mov dx,0
mov bx,100h
div bx 
;除法溢出
mov ax,0123h
mov bh,1
;int 00h 一条隐形的由硬件产生的指令
;调用系统函数：打出一句话（divide overflow/divide by zero并且强制退出）
div bh
;会产生异常

;浮点处理器
;fadd fsub fmul fdiv小数的加减乘除
;由浮点数处理器负责执行
pi dd 3.14;32位小数，相当于float
r dq 3.1415926;64位小数，相当于double
v dt 3.14159125;80位小数，10字节 long double

;cpu内部有8个小数寄存器，分别叫做
st(0) st(1) ... st(7)
;其中st(0)简称st
;这8个寄存器的宽度均达到80位

;样例
fld abc;把3.14载入st(0) float load
fld xyz;abc(3.14)->st(1)  xyz(2.0)->st(0)
fmul st,st(1);st = st * st(1)
fstp result;st(0)->result st(0)->rubbish bin st(1)->st(0) 
fstp st;然后小数堆栈就变成空了

;逻辑运算指令:TEST
mov ax,9234h
test ax,8000h;ax and 8000h然而不改变ax的值，然而影响标志位
;zf = 0, ax = 9234h
jnz msb_is_one
;test和and的关系相当于cmp和sub的关系

;移位指令
;sal \equiv shl shift arithmatic left 
;sar shift arithmatic right 如果是负数的左边要补1如果是正数则补0
;rcl  
;rcr
;shr shl ror rol会改变cf的值

```

