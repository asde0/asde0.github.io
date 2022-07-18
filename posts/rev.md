---
layout: default
title: "rev"
permalink: /rev/
---

# asm_code 

```
│           0x00001530      e86bfcffff     call sym.imp.read           ; ssize_t read(int fildes, void *buf, size_t nbyte)
│           0x00001535      c745ec000000.  mov dword [var_14h], 0
│       ┌─< 0x0000153c      eb42           jmp 0x1580
│       │   ; CODE XREF from main @ 0x1584(x)
│      ┌──> 0x0000153e      8b45ec         mov eax, dword [var_14h]
│      ╎│   0x00001541      4898           cdqe
│      ╎│   0x00001543      0fb64405f2     movzx eax, byte [rbp + rax - 0xe]
│      ╎│   0x00001548      8845ea         mov byte [var_16h], al
│      ╎│   0x0000154b      b804000000     mov eax, 4
│      ╎│   0x00001550      2b45ec         sub eax, dword [var_14h]
│      ╎│   0x00001553      4898           cdqe
│      ╎│   0x00001555      0fb64405f2     movzx eax, byte [rbp + rax - 0xe]
│      ╎│   0x0000155a      8845eb         mov byte [var_15h], al
│      ╎│   0x0000155d      8b45ec         mov eax, dword [var_14h]
│      ╎│   0x00001560      4898           cdqe
│      ╎│   0x00001562      0fb655eb       movzx edx, byte [var_15h]
│      ╎│   0x00001566      885405f2       mov byte [rbp + rax - 0xe], dl
│      ╎│   0x0000156a      b804000000     mov eax, 4
│      ╎│   0x0000156f      2b45ec         sub eax, dword [var_14h]
│      ╎│   0x00001572      4898           cdqe
│      ╎│   0x00001574      0fb655ea       movzx edx, byte [var_16h]
│      ╎│   0x00001578      885405f2       mov byte [rbp + rax - 0xe], dl
│      ╎│   0x0000157c      8345ec01       add dword [var_14h], 1
│      ╎│   ; CODE XREF from main @ 0x153c(x)
│      ╎└─> 0x00001580      837dec01       cmp dword [var_14h], 1
│      └──< 0x00001584      7eb8           jle 0x153e
│           0x00001586      488d3d7b0d00.  lea rdi, str.Checking_the_received_license_key__n
```
Input gets mangled somehow...
> each jump happens once 

