Tags : 汇编语言


### 检测点1.1
1. 13
>一根地址线指向1Byte(8bit), $8KB = 2^3 * 2^{10}B$
2. 1024 0 1023
3. 8 * 1024 1024
4. $2^{30}, 2^{20}, 2^{10}$
5. 64 1 16 4
> $1KB = 2^{10}B $
$1MB = 2^{20}B$
$1GB = 2^{30}B$

6. 1 1 2 2 4
> $2^3b = 1B$

7. 512 256
> 8086一次可以读入2B， 1024要读512次。80386一次读入4B， 要读取256次

8. 二进制

### 检测点2.1
1. 写值
```
mov ax,62627 AX=F4A3H 
mov ah,31H AX=31A3H 
mov al,23H AX=3123H 
add ax,ax AX=6246H 
mov bx,826CH BX=826CH 
mov cx,ax CX=6246H 
mov ax,bx AX=826CH 
add ax,bx AX=04D8H 
mov al,bh AX=0482H 
mov ah,bl AX=6C82H
add ah,ah AX=D882H 
add al,6 AX=D888H 
add al,al AX=D810H 
mov ax,cx AX=6246H
```

2. 
```
mov ax, 2
add ax, ax
add ax, ax
add ax, ax
```

### 检测点2.2
1. 00010H-1000FH
> 0001H * 16 + FFFFH

2. 最小10001H， 最大2000H
> 20000H - FFFFH = 10001H 所以SA * 16 >= 10001H

### 检测点2.3
>执行了三条语句，就进行了3次修改，最后一条语句又进行了一次修改，一共修改了4次，最后IP为0000H

### 检测点3.1
1. 
```
2262H
E626H
E626H
2662H
D6E6H
FD48H
2c14H
0000H
00E6H
0000H
0026H
000CH
```
2. 
```
mov ax,6622H
jmp 0ff0:0100
mov ax,2000H
mov ds,ax
mov ax,[0008]
mov ax,[0002]

```
|语句|CS|IP|DS|AX|BX|
|:-:|:-:|:-:|:-:|:-:|:-:|
|mov ax,6622H|2000H|0003|1000H|6622H|0000|
|jmp 0ff0:0100|0ff0H|0100H|1000H|6622H|0000|
|mov ax,2000H|0ff0H|0103H|1000H|2000H|0000|
|mov ds,ax|0ff0H|0105H|2000H|2000H|0000|
|mov ax,[0008]|0ff0H|0008H|2000H|C389H|0000|
|mov ax,[0002]|0ff0H|000BH|2000H|EA66H|0000|

3.
>数据和程序在计算机中都是以二进制的形式存放的，在区别程序和数据时，关键是看段地址，如果段地址是ds段，说明该内存中存放的是数据，如果段地址是cs段，说明该内存中存放的是程序指令 
### 检测点3.2
1. 
```
mov ax,2000H
mov ss,ax
mov sp,10H
```
2. 
```
mov ax,1000H
mov ss,ax
mov sp,0
```

### 实验2
1. 
```
ax = D4EA
ax = B9EE
bx = 300F
bx = 5F46
sp = 00FE; 修改的内存单元的地址是220FE, 内容为B9EE
sp = 00FC; 修改的内存单元的地址是220FC, 内容为5F46
sp = 00FE; ax = 5F46
sp = 0100; bx = B9EE
sp = 00FE; 修改的内存单元的地址是220FE, 内容为300F
sp = 00FC; 修改的内存单元的地址是220FC, 内容为2F37

```
2.
>不明白

### 实验4
1

 
> 修改段地址，使偏移地址从0开始
```
assume cs:code

code segment

    mov ax,0020h
    mov ds,ax
    
    mov bx,0
    
    mov cx,64
    
s:  mov [bx],bx
    inc bx
    loop s
    
    mov ax,4c00h
    int 21h

code ends

end

```

3
> 第一空指明数据从cs开始复制，第二空假定为1，然后生成.exe文件，使用u来观察代码的存储情况 观察到:
```
2B29:0017 B8004c MOV AX,4C00
```
可以得出复制偏移地址从0-16,所以cx为17
```
assume cs:code
code segment

    mov ax,cs
    mov ds,ax
    mov ax,0020h
    mov es,ax
    mov bx,0
    mov cx,17h
s:  mov al,[bx]
    mov es:[bx],al
    inc bx
    loop s
    mov ax,4c00h
    int 21h
code ends
end
```

### 检测点6.1
1. 
```
assume cs:codesg

codesg segment
    dw 0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,0987h
    
start: mov ax,0
       mov ds,ax
       mov bx,0
       
       mov cx,8
    s: mov ax,[bx]
       mov cs:[bx],ax
       add bx,2
       loop s
       
       mov ax,4c00h
       int 21h

codesg ends

end start

```

2. 
```
assume cs:codesg

codesg segment
    dw 0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,0987h
    dw 0,0,0,0,0,0,0,0,0,0
    
start: mov ax,cs
       mov ss,ax
       mov sp,24h   ;代码前面有18个字,36个字节
        
       mov ax,0
       mov ds,ax
       mov bx,0
       
       mov cs,8
    s: push [bx]
       pop ss:[bx]
       add bx,2
       loop s
       
       mov ax,4c00h
       int 21h

codesg ends

end start
    
```

