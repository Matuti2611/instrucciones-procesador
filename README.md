'''

0xC00800FF - ADDI $a0, $zero, 255 -> R[4] = 0x000000FF.
0xC00A0A01 - ADDI $a1, $zero, 2561 ->  R[5] = 0x00000A01.
0x010C801C - ADD $v0, $a0, $a1 -> R[8] = 0x00000B00
0x010A901D - SUB $v1, $a0, $a1 -> R[9] = 0xFFFFF6FE
'''
