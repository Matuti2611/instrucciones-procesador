## Caso 1 - Operaciones aritméticas básicas

### Descripción

Testeo de operaciones aritméticas básicas entre registros (suma, resta y multiplicación de 32 bits) y carga de constantes numéricas inmediatas positivas mediante extensión de signo.

### Instrucciones

`ADDI`, `ADD`, `SUB`, `MUL`

### Precondiciones

- Inicializar el procesador en un estado limpio.
- Disponer de los registros `$zero` ($R[0]$), `$a0` ($R[4]$), `$a1` ($R[5]$), `$v0` ($R[8]$) y `$v1` ($R[9]$) habilitados para lectura y escritura.

### Código

```text
0xC00800FF - ADDI $zero, $a0, 255 -> R[4] = 0x000000FF
0xC00A0A01 - ADDI $zero, $a1, 2561 -> R[5] = 0x00000A01
0x010A801C - ADD $a0, $a1, $v0 -> R[8] = 0x00000B00
0x010A901D - SUB $a0, $a1, $v1 -> R[9] = 0xFFFFF6FE
0x010A8015 - MUL $a0, $a1, $v0 -> R[8] = 0x0009F6FF
```

### Postcondiciones

- Visualizar en el panel de registros que `$a0` ($R[4]$) almacena `0x000000FF` y `$a1` ($R[5]$) almacena `0x00000A01`.
- Verificar que el registro de destino `$v0` ($R[8]$) cambie secuencialmente reflejando primero la suma (`0x00000B00`) y finalmente la multiplicación entera (`0x0009F6FF`).
- Comprobar que `$v1` ($R[9]$) contiene el valor en complemento a dos `0xFFFFF6FE`.

### Conclusión

Anduvo. Las operaciones aritméticas mapearon correctamente los registros fuente y el procesador decodificó de manera exitosa los inmediatos aritméticos de las instrucciones tipo I.

---

## Caso 2 - Operaciones lógicas, multiplicación alta y comparaciones

### Descripción

Testeo de operaciones lógicas de inicialización por máscara limpia (`XOR`), sumas de inmediatos con extensión de signo negativa, multiplicación de 64 bits y comparaciones (`SLT`, `SLTU`, `SLTI`, `SLTIU`).

### Instrucciones

`XOR`, `ADDI`, `MULH`, `MULHU`, `SLT`, `SLTU`, `SLTI`, `SLTIU`

### Precondiciones

- Contar con registros previamente cargados con datos residuales para comprobar que la limpieza mediante `XOR` funcione correctamente.

### Código

```text
0x0108400A - XOR $a0, $a0, $a0 -> R[4] = 0x00000000
0x014A500A - XOR $a1, $a1, $a1 -> R[5] = 0x00000000
0xC009FFFF - ADDI $zero, $a0, -1 -> R[4] = 0xFFFFFFFF
0xC00A0002 - ADDI $zero, $a1, 2 -> R[5] = 0x00000002
0x010A9016 - MULH  $a0, $a1, $v1 -> R[9] = 0xFFFFFFFF
0x010A5017 - MULHU $a0, $a1, $v0 -> R[8] = 0x00000001
0x010A800C - SLT $a0, $a1, $v0 -> R[8] = 0x00000001
0x010A900D - SLTU $a0, $a1, $v1 -> R[9] = 0x00000000
0xB1100000 - SLTI $a0, $v0, 0 -> R[8] = 0x00000001
0xB9120000 - SLTIU $a0, $v1, 0 -> R[0] = 0x00000000
```

### Postcondiciones

- Verificar la limpieza inicial de `$a0` y `$a1`.
- Confirmar la correcta extensión de signo del inmediato `-1`.
- Validar los resultados de `MULH` y `MULHU`.
- Comprobar el funcionamiento correcto de las comparaciones con y sin signo.

### Conclusión

Anduvo. El procesador diferencia correctamente las operaciones con signo de las operaciones sin signo.

---

## Caso 3 - Saltos absolutos

### Descripción

Testeo de instrucciones de salto absoluto (`J`) y salto con enlace (`JAL`).

### Instrucciones

`J`, `JAL`

### Precondiciones

- Colocar el `PC` inicial en `0x00000000`.

### Código

```text
0x10000004 - J 0x0000004 -> PC = 0x00000010
0x18000004 - JAL 0x0000004 -> PC = 0x00000010, R[31] = 0x00000004
```

### Postcondiciones

- Verificar el cambio del `PC`.
- Confirmar que `JAL` almacene la dirección de retorno en `$ra`.

### Conclusión

Anduvo. Los saltos modifican correctamente el flujo del programa y `JAL` almacena el retorno.

---

## Caso 4 - AND inmediatos

### Descripción

Testeo de `ANDI` y `ANDIH`.

### Instrucciones

`ANDI`, `ANDIH`

### Precondiciones

```text
R[4] = 0xFFFFFFFF
```

### Código

```text
0x211000F0 - ANDI $a0, $v0, 0x00F0 -> R[8] = 0x000000F0
0x211300F0 - ANDIH $a0, $v1, 0x00F0 -> R[9] = 0x00F00000
```

### Postcondiciones

- Validar el resultado de `ANDI`.
- Validar el resultado de `ANDIH`.

### Conclusión

Anduvo. Las operaciones lógicas inmediatas funcionan correctamente.

---

## Caso 5 - OR, XOR y LUI

### Descripción

Testeo de operaciones OR, XOR y carga inmediata superior.

### Instrucciones

`ORI`, `ORIH`, `XORI`, `XORIH`, `LUI`

### Precondiciones

```text
R[4] = 0xAAAA0000
```

### Código

```text
0x29105555 - ORI $a0, $v0, 0x5555 -> R[8] = 0xAAAA5555
0x2912AAAA - ORIH $a0, $v1, 0xAAAA -> R[9] = 0xAAAA0000
0x3110FFFF - XORI $a0, $v0, 0xFFFF -> R[8] = 0xAAAAFFFF
0x3112AAAA - XORIH $a0, $v1, 0xAAAA -> R[9] = 0x00000000
0x38085555 - LUI $v0, 0x5555 -> R[8] = 0x55550000
```

### Postcondiciones

- Verificar todos los resultados esperados.
- Confirmar el funcionamiento de `LUI`.

### Conclusión

Anduvo. Todas las operaciones booleanas inmediatas produjeron los resultados esperados.

---

## Caso 6 - Memoria

### Descripción

Testeo de operaciones de carga y almacenamiento en memoria.

### Instrucciones

`SW`, `LW`, `SH`, `LH`, `LHU`, `SB`, `LB`, `LBU`

### Precondiciones

```text
R[4] = 0x000000FF
```

### Código

```text
0x48080020 - SW $zero, $a0, 0x20 -> M[0x20] = 0x000000FF
0x40100020 - LW $zero, $v0, 0x20 -> R[8] = 0x000000FF
0x50080020 - SH $zero, $a0, 0x20 -> M[0x20][15:0] = 0x00FF
0x60100020 - LH $zero, $v0, 0x20 -> R[8] = 0x000000FF
0x68120020 - LHU $zero, $v1, 0x20 -> R[9] = 0x000000FF
0x58080020 - SB $zero, $a0, 0x20 -> M[0x20][7:0] = 0xFF
0x70100020 - LB $zero, $v0, 0x20 -> R[8] = 0xFFFFFFFF
0x78120020 - LBU $zero, $v1, 0x20 -> R[9] = 0x000000FF
```

### Postcondiciones

- Verificar los datos escritos en memoria.
- Confirmar el funcionamiento de la extensión de signo y extensión por cero.

### Conclusión

Anduvo. Las operaciones de memoria funcionan correctamente.

---

## Caso 7 - Branches

### Descripción

Testeo de bifurcaciones condicionales.

### Instrucciones

`BEQ`, `BNE`, `BLT`, `BGT`, `BLE`, `BGE`

### Precondiciones

```text
R[4] = 0x0000000A
R[5] = 0x00000005
PC = 0x00000000
```

### Código

```text
0x81050004 - BEQ $a0, $a1, 4 -> PC = 0x00000004
0x89050004 - BNE $a0, $a1, 4 -> PC = 0x00000014
0x91050004 - BLT $a0, $a1, 4 -> PC = 0x00000004
0x99050004 - BGT $a0, $a1, 4 -> PC = 0x00000014
0xA1050004 - BLE $a0, $a1, 4 -> PC = 0x00000004
0xA9050004 - BGE $a0, $a1, 4 -> PC = 0x00000014
```

### Postcondiciones

- Verificar el comportamiento correcto del `PC` para cada condición.

### Conclusión

Anduvo. La lógica de branches responde correctamente.

---

## Caso 8 - Shift

### Descripción

Testeo de desplazamientos lógicos y aritméticos.

### Instrucciones

`SLL`, `SRL`, `SRA`, `SLLR`, `SRLR`, `SRAR`

### Precondiciones

```text
R[4] = 0x0000000F
R[5] = 0x00000002
```

### Código

```text
0x00244080 - SLL $a0, $v0, 2 -> R[8] = 0x0000003C
0x00244081 - SRL $a0, $v0, 2 -> R[8] = 0x00000003
0x00244082 - SRA $a0, $v0, 2 -> R[8] = 0x00000003
0x00A44003 - SLLR $a1, $a0, $v0 -> R[8] = 0x0000003C
0x00A44004 - SRLR $a1, $a0, $v0 -> R[8] = 0x00000003
0x00A44005 - SRAR $a1, $a0, $v0 -> R[8] = 0x00000003
```

### Postcondiciones

- Verificar los desplazamientos hacia izquierda y derecha.
- Confirmar el funcionamiento de las variantes dinámicas.

### Conclusión

Anduvo. El barrel shifter opera correctamente.

---

## Caso 9 - Lógica entre registros

### Descripción

Testeo de operaciones `AND`, `OR` y `NOR`.

### Instrucciones

`AND`, `OR`, `NOR`

### Precondiciones

```text
R[4] = 0x5A3C96E7
R[5] = 0x3C78A5F1
```

### Código

```text
0x00854008 - AND $a0, $a1, $v0 -> R[8] = 0x183884E1
0x00854009 - OR $a0, $a1, $v0 -> R[8] = 0x7E7CD7F7
0x0085400B - NOR $a0, $a1, $v0 -> R[8] = 0x81832808
```

### Postcondiciones

- Verificar los resultados de las operaciones lógicas.

### Conclusión

Anduvo. La ALU resuelve correctamente las operaciones lógicas.

---

## Caso 10 - Saltos por registro

### Descripción

Testeo de `JR` y `JALR`.

### Instrucciones

`JR`, `JALR`

### Precondiciones

```text
R[4] = 0x00002000
PC = 0x00000000
```

### Código

```text
0x0080001E - JR $a0 -> PC = 0x00002000
0x0085001F - JALR $a0, $a1 -> R[5] = 0x00000004, PC = 0x00002000
```

### Postcondiciones

- Verificar el cambio del `PC`.
- Confirmar el almacenamiento del retorno en `JALR`.

### Conclusión

Anduvo. Los saltos indirectos funcionan correctamente.

---

## Caso 11 - División y resto

### Descripción

Testeo de división entera y resto con y sin signo.

### Instrucciones

`DIV`, `DIVU`, `REST`, `RESTU`

### Precondiciones

```text
R[4] = 0x00000017
R[5] = 0x00000005
```

### Código

```text
0x00854018 - DIV $a0, $a1, $v0 -> R[8] = 0x00000004
0x00854019 - DIVU $a0, $a1, $v0 -> R[8] = 0x00000004
0x0085401A - REST $a0, $a1, $v0 -> R[8] = 0x00000003
0x0085401B - RESTU $a0, $a1, $v0 -> R[8] = 0x00000003
```

### Postcondiciones

- Verificar el cociente.
- Verificar el resto.

### Conclusión

Anduvo. La unidad de división calcula correctamente cociente y residuo.

---

## Caso 12 - Cargas indexadas

### Descripción

Testeo de cargas indexadas mediante suma entre registro base y registro índice.

### Instrucciones

`LWX`, `LHX`, `LHUX`, `LBX`, `LBUX`

### Precondiciones

```text
R[4] = 0x000000FF
R[5] = 0x00000010
```

### Código

```text
0x00105014 - LWX  $zero, $v0, $a1 -> R[8] = 0x000000FF
0x00105010 - LHX  $zero, $v0, $a1 -> R[8] = 0x000000FF
0x00125011 - LHUX $zero, $v1, $a1 -> R[9] = 0x000000FF
0x00105012 - LBX  $zero, $v0, $a1 -> R[8] = 0xFFFFFFFF
0x00125013 - LBUX $zero, $v1, $a1 -> R[9] = 0x000000FF
```

### Postcondiciones

- Verificar el cálculo correcto de la dirección efectiva.
- Confirmar la correcta extensión de signo en `LBX`.

### Conclusión

Anduvo. Las cargas indexadas calculan correctamente la dirección efectiva y producen los resultados esperados.
