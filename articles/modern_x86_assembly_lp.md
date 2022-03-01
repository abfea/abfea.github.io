---
title: 现代x86汇编语言程序设计
---

## 基础知识

### 通用寄存器

### 指令操作数

```asm
; x32
; 立即数
mov  eax, 42            ; eax  = 42
imul ebx, 11h           ; ebx *= 0x11
xor  dl,  55h           ; dl  ^= 0x55
add  esi, 8             ; esi += 8

; 寄存器
mov eas, ebx            ; eax  = ebx
inc ecx                 ; exc += 1
add ebx, esi            ; ebx +=esi
mul ebx                 ; edx:eax = eax*ebx 

; 内存
mov eax, [ebx]          ; eax = *ebx
add eax, [val1]         ; eax += *val1
or  ecx, [ebx+esi]      ; exc |= *(ebx+esi)
sub word ptr [edi], 12  ; *(short*)edi -= 12

; x64 使用不同长度操作数的x86-64指令示例

; 8bit
add al, bl
cmp dl, [r15]
mul r10b
or  [r8+rdi], al
shl r9b, cl

; 16bit
add ax, bx
cmp dx, [r15]
mul r10w
or  [r8+rdi*2], ax
shl r9w, cl

; 32bit
add eax, ebx
cmp edx, [r15]
mul r10d
or  [r8+rdi*4], eax
shl r9d, cl

; 64bit 
add rax, rbx
cmp rdx, [r15]
mul r10
or  [r8+rdi*8], rax
shl r9, cl

```

### 内存寻址方式

Effective Address = BaseReg + IndexReg*ScaleFactor + Displacement  
实际地址 = 基址寄存器 + 索引寄存器*放大因子 + 位移

```asm
; x32
mov eax, [MyVa]              ; Disp
mov eax, [ebx]               ; BaseReg
mov eax, [ebx+12]            ; BaseReg + Disp
mov eax, [MyArray+esi*4]     ; IndexReg * SF + Disp
mov eax, [ebx+esi]           ; BaseReg + IndexReg
mov eax, [ebx+esi+12]        ; BaseReg + IndexReg + Disp
mov eax, [ebx+esi*4]         ; BaseReg + IndexReg * SF
mov eax, [ebx+esi*4+20]      ; BaseReg + IndexReg * SF + Disp
; x64
mov rax, [MyVa]              ; RIP + Disp
mov rax, [rbx]               ; BaseReg
mov rax, [rbx+16]            ; BaseReg + Disp
mov rax, [r15*8+48]          ; IndexReg * SF + Disp
mov rax, [rbx+r15]           ; BaseReg + IndexReg
mov rax, [rbx+r15+15]        ; BaseReg + IndexReg + Disp
mov rax, [rbx+r15*8]         ; BaseReg + IndexReg * SF
mov rax, [rbx+r15*8+64]      ; BaseReg + IndexReg * SF + Disp
```

### 条件码、助记后缀和测试条件

```plain
Above                      A      CF==0&&ZF==0
Neither below or equal     NBE   
Above or equal             AE     CF==0
Not below                  NB
Blow                       B      CF==1
Neither above nor equal    NAE   
Below or equal             BE     CF==1||ZF==1
Not Above                  NA     
Equal                      E      ZF==1
Not equal                  NE     ZF==0
Zero                       Z
Not zero                   NZ
Greater                    G      ZF==0&&SF==OF
Neither less nor equal     NLE    
Greater or equal           GE     SF==OF
Less                       L      SF!=OF
Not less                   NL
Neither greater nor equal  NGE    
Less or equal              LE     ZF==1||SF!=OF
Not Greater                NG     
Sign                       S      SF==1
Not sign                   NS     SF==0
Carry                      C      CF==1
Not carry                  NC     CF==0
Overflow                   O      OF==1
Not overflow               NO     OF==0
Parity even                PE     
Not Parity                 NP     PE==0
Parity odd                 PO
```

### x86-64 新指令

```asm
cdqe            ; 对EAX中的双字值进行符号扩展并将结果保存到RAX

cmpsb           ; 比较寄存器RSI和RDI指向和内存地址中的值，
cmpsw           ; 设置状态标志来指示结果
cmpsd
cmpsq

cmpxchg16b      ; 用16位内存操作数比较RDX:RAX, 根据结果进行交换
cqo             ; 将RAX的内容符号扩展到RDX:RAX
jrcxz           ; 如果条件RCX==0为真跳转到指定的内存地址

lodsb           ; 将RSI指向的内存地址中的值加载到AL、AX、EAX或RAX寄存器
lodsw
lodsd
lodsq

movsb           ; 将寄存器RSI指定的内存地址中的值拷贝到寄存器RDI指定的内存地址
movsw
movsd
movsq

pop
popfq

push
pushfq

rep
repe/repz
repne/repnz

scasb
scasw
scasd
scasq

stosb
stosw
stosd
stosq

```

### 64位易变和非易变寄存器

* General-purpose:
  * 易变寄存器: RAX, RCX RDX, R8, R9, R10, R11
  * 非易变寄存器: RBX, RSI, RDI, RBP, RSP, R12~R15
* X86-SSE XMM:
  * 易变寄存器: XMM0~XMM5
  * 非易变寄存器: XMM6~XMM15

## x64汇编

在x86-64的Unix系统上， 最开始的六个函数参数将分别写入:  
**edi, esi, edx, ecx, e8d, e9d**

```asm
main:
   0x000000000040117e <+26>:    push   0xa
   0x0000000000401180 <+28>:    push   0x9
   0x0000000000401182 <+30>:    push   0x8
   0x0000000000401184 <+32>:    push   0x7
   0x0000000000401186 <+34>:    mov    r9d,0x6
   0x000000000040118c <+40>:    mov    r8d,0x5
   0x0000000000401192 <+46>:    mov    ecx,0x4
   0x0000000000401197 <+51>:    mov    edx,0x3
   0x000000000040119c <+56>:    mov    esi,0x2
   0x00000000004011a1 <+61>:    mov    edi,0x1
   0x00000000004011a6 <+66>:    call   0x40111a <bar(int, int, int, int, int, int, int, int, int, int)>
   0x00000000004011ab <+71>:    add    rsp,0x20
   0x00000000004011af <+75>:    mov    DWORD PTR [rbp-0x8],eax
   0x00000000004011b2 <+78>:    mov    eax,0x0
   0x00000000004011b7 <+83>:    leave
   0x00000000004011b8 <+84>:    ret

bar:
   0x000000000040111a <+0>:     push   rbp
   0x000000000040111b <+1>:     mov    rbp,rsp
   0x000000000040111e <+4>:     mov    DWORD PTR [rbp-0x4],edi
   0x0000000000401121 <+7>:     mov    DWORD PTR [rbp-0x8],esi
   0x0000000000401124 <+10>:    mov    DWORD PTR [rbp-0xc],edx
   0x0000000000401127 <+13>:    mov    DWORD PTR [rbp-0x10],ecx
   0x000000000040112a <+16>:    mov    DWORD PTR [rbp-0x14],r8d
   0x000000000040112e <+20>:    mov    DWORD PTR [rbp-0x18],r9d
   0x0000000000401132 <+24>:    mov    edx,DWORD PTR [rbp-0x4]
   0x0000000000401135 <+27>:    mov    eax,DWORD PTR [rbp-0x8]
   0x0000000000401138 <+30>:    add    edx,eax
   0x000000000040113a <+32>:    mov    eax,DWORD PTR [rbp-0xc]
   0x000000000040113d <+35>:    add    edx,eax
   0x000000000040113f <+37>:    mov    eax,DWORD PTR [rbp-0x10]
   0x0000000000401142 <+40>:    add    edx,eax
   0x0000000000401144 <+42>:    mov    eax,DWORD PTR [rbp-0x14]
   0x0000000000401147 <+45>:    add    edx,eax
   0x0000000000401149 <+47>:    mov    eax,DWORD PTR [rbp-0x18]
   0x000000000040114c <+50>:    add    edx,eax
   0x000000000040114e <+52>:    mov    eax,DWORD PTR [rbp+0x10]
   0x0000000000401151 <+55>:    add    edx,eax
   0x0000000000401153 <+57>:    mov    eax,DWORD PTR [rbp+0x18]
   0x0000000000401156 <+60>:    add    edx,eax
   0x0000000000401158 <+62>:    mov    eax,DWORD PTR [rbp+0x20]
   0x000000000040115b <+65>:    add    edx,eax
   0x000000000040115d <+67>:    mov    eax,DWORD PTR [rbp+0x28]
   0x0000000000401160 <+70>:    add    eax,edx
   0x0000000000401162 <+72>:    pop    rbp
   0x0000000000401163 <+73>:    ret



(gdb) x/30x $rbp-0x18
0x7fffffffd888: 0x00000006  0x00000005  0x00000004  0x00000003
0x7fffffffd898: 0x00000002  0x00000001  0xffffd8e0  0x00007fff
0x7fffffffd8a8: 0x004011ab  0x00000000  0x00000007  0x00000000
0x7fffffffd8b8: 0x00000008  0x00000000  0x00000009  0x00000000
0x7fffffffd8c8: 0x0000000a  0x00000000  0xffffd9d0  0x00007fff
0x7fffffffd8d8: 0x00000000  0x00000003  0x00000000  0x00000000
0x7fffffffd8e8: 0xf7e01b75  0x00007fff  0xffffd9d8  0x00007fff
0x7fffffffd8f8: 0x00000064  0x00000001

(gdb) p $rsp
$32 = (void *) 0x7fffffffd8a0

(gdb) p $rbp
$33 = (void *) 0x7fffffffd8a0

0x7fffffffd888:     0x00000006  0x00000005 -- [rbp-0x18]
                    0x00000004  0x00000003
0x7fffffffd898:     0x00000002  0x00000001

0x7fffffffd8a0:     0xffffd8e0  0x00007fff -- $rsp & $rbp

0x7fffffffd8a8:     0x004011ab  0x00000000
                    0x00000007  0x00000000 -- [rbp+0x10] d8a0+0x10=d8b0
0x7fffffffd8b8:     0x00000008  0x00000000
                    0x00000009  0x00000000
0x7fffffffd8c8:     0x0000000a  0x00000000 -- [rbp+0x28]

```


