```assembly
pushfd ;把32位寄存器压入堆栈
popfd

cbw ;convert byte to word
cwd ;convert word to double word
cdq ;convert double word to quadruple word
;符号扩充

mov al,0feh
cbw ;参数是隐含的，把al扩充成ax，故运算结果一定在ax中，ax=0fffeh
mov ax,8000h
cwd ;dx=0ffffh,ax=8000h
mov eax,0abcd1234h
cdq ;eax不变 edx=0ffffffffh

;〇扩充
movzx ax,al;zx:zero extension
movzx eax,al
movzx ebx,cx
;符号扩充
movsx ax,al;sx:sign extension

;换码指令：xlat (translate)查表指令
;xlat执行前必须让ds:bx指向表，al必须赋值为数组的下表
;执行xlat之后，al=ds:[bx+al]

;use16表示使用16位偏移地址
.386
assume ds:data cs:code
data segment use16
	t db "0123456789"
	x dd 217483647
data ends
code segment use16
main:
	mov ax,data
	mov ds,ax
	mov bx,offset t
	mov ecx,8
	mov eax,x
next:
	rol eax,4
	push eax
	and eax,0fh
	xlat
	mov ah,2
	mov dl,al
	int 21h;输出dl中的单个字符
	pop eax
	sub ecx,1
jnz next
mov ah,4ch
int 21h
code ends
end main

;算术指令
;inc:increment inc指令不会改变cf add指令不会影响cf
;adc:add with carry
;12345678h+5678ffffh
mov dx,1234h
mov ax,5678h
add ax,0ffffh
adc dx,5678h ;dx=dx+5678h+cf
;减法指令
;dec:decrement 不影响cf，会影响zf
mov ax,1
dec ax;zf=1
;neg
mov ax,1
neg ax;ax=0ffffh相当于减法0-ax
;neg ax=(not ax)+1
;sbb:subtract with borrow
;56781234h-1111ffffh
mov ax,1234h
sub ax,0ffffh
mov dx,5678h
sbb dx,1111h;dx=5678h-1111h-cf

;cmp丢弃差值，保留标志位的改变
cmp ax,bx
jb ax_is_below_bx;cf=1则跳
jc ax_is_below_bx;cf=1则跳
;因此jc\equiv jb
;那符号呢？
mov ax,1
mov bx,0ffffh
cmp ax,bx
jb ax_is_below_bx
;(1)ja,jb,jae,jbe非符号数
;jb:cf=1
;ja:cf=0且zf=0

;(2)jg,jl,jge,jle符号数
;jg:sf==of
;	没有溢出而且差为正
;	溢出了
		mov ah,7fh
		mov bh,80h
		cmp ah,bh;ah-bh=0ffh,sf=1,of=1
		
;jl:sf!=of
;	sf=1,of=0
		mov ax,2
		mov bx,3
		cmp ax,bx;ax-bx=0ffffh,sf=1,of=0
;	sf=0,of=1
		mov ah,80h
		mov bh,7fh
		cmp ah,bh;ah-bh=1,sf=0,of=1
		
;jg:jump if greater than
;jl:jump if less than


```

