```
0xC00800FF - ADDI $a0, $zero, 255 -> R[4] = 0x000000FF.
0xC00A0A01 - ADDI $a1, $zero, 2561 ->  R[5] = 0x00000A01.
0x010C801C - ADD $v0, $a0, $a1 -> R[8] = 0x00000B00
0x010A901D - SUB $v1, $a0, $a1 -> R[9] = 0xFFFFF6FE
0x010A8015 - MUL  $v0, $a0, $a1 -> R[8] = 0x0009F6FF

0x0108400A - XOR $a0, $a0, $a0 -> R[4] = 0x00000000
0x014A500A - XOR $a1, $a1, $a1 -> R[5] = 0x00000000
0xC009FFFF - ADDI $a0, $zero, -1 -> R[4] = 0xFFFFFFFF
0xC00A0002 - ADDI $a1, $zero, 2 -> R[5] = 0x00000002
0x010A9016 - MULH  $v1, $a0, $a1 -> R[9] = 0xFFFFFFFF
0x010A5017 - MULHU $t2, $a0, $a1 -> R[8] = 0x00000001 (overflow)
```
