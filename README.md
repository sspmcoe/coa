# COA Practicals (8086 MASM Assembly Language)

### Commands to Run in DOSBox (MASM & DEBUG)

```dos
MOUNT C C:\8086
C:
MASM FILENAME.ASM;
LINK FILENAME.OBJ;
DEBUG FILENAME.EXE
```

---

## Experiment 2: Basic ALU Operations (Addition, Subtraction, Multiplication, Division)
**Aim:** To implement and execute basic ALU operations—Addition, Subtraction, Multiplication, and Division using MASM (Microsoft Macro Assembler) and observe the result using DEBUG.

```assembly
; Filename: ARITH.ASM
.MODEL SMALL
.STACK 100H
.DATA
    A DB 10
    B DB 2
    SUM DB ?
    SUBT DB ?
    MULR DB ?
    DIVR DB ?
.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX

    ; Addition
    MOV AL, A
    ADD AL, B
    MOV SUM, AL

    ; Subtraction
    MOV AL, A
    SUB AL, B
    MOV SUBT, AL

    ; Multiplication
    MOV AL, A
    MOV BL, B
    MUL BL
    MOV MULR, AL

    ; Division
    MOV AL, A
    MOV AH, 0
    MOV BL, B
    DIV BL
    MOV DIVR, AL

    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

---

## Experiment 3: Number Conversion Techniques (HEX to BCD & ASCII to BCD)
**Aim:** To write and execute 8086 assembly language programs in MASM to perform number system conversions:
1. Hexadecimal to BCD
2. ASCII to BCD

### (A) HEX to BCD Conversion

```assembly
.MODEL SMALL
.STACK 100H
.DATA
    NUM DB 7BH ; Hex value = 7B = 123 Decimal
    BCD DB 3 DUP(?)
.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX
    MOV AL, NUM
    MOV AH, 00
    MOV SI, OFFSET BCD
    MOV BL, 10
L1:
    DIV BL      ; AX / 10
    ADD AH, 30H ; Convert remainder to ASCII
    MOV [SI], AH
    INC SI
    MOV AH, 00
    CMP AL, 00
    JNE L1

    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

### (B) ASCII to BCD Conversion

```assembly
.MODEL SMALL
.STACK 100H
.DATA
    ASCIICHAR DB '7'
    BCDVALUE DB ?
.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX
    MOV AL, ASCIICHAR
    SUB AL, 30H ; Convert ASCII to BCD
    MOV BCDVALUE, AL

    ; Exit
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

---

## Experiment 4: String Comparison Operations
**Aim:** Implement String comparison Operations in 8086 through the utilities provided by DOS interrupts (MASM).

```assembly
.MODEL SMALL
.STACK 100H
.DATA
    STR1 DB 'AM$'
    STR2 DB 'PM$'
    MSG1 DB 13,10,'STRINGS ARE EQUAL$'
    MSG2 DB 13,10,'STRINGS ARE NOT EQUAL$'
.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX
    LEA SI, STR1
    LEA DI, STR2
COMPARE:
    MOV AL, [SI]
    MOV BL, [DI]
    CMP AL, BL
    JNE NOT_EQUAL
    CMP AL, '$'
    JE EQUAL
    INC SI
    INC DI
    JMP COMPARE
EQUAL:
    MOV AH, 09H
    LEA DX, MSG1
    INT 21H
    JMP EXIT
NOT_EQUAL:
    MOV AH, 09H
    LEA DX, MSG2
    INT 21H
EXIT:
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

---

## Experiment 5: Block Exchange Operations
**Aim:** To implement Block Exchange operations using SI (Source Index) and DI (Destination Index) registers in the 8086 microprocessor.

```assembly
.MODEL SMALL
.STACK 100H
.DATA
    BLOCK1 DB 11H, 22H, 33H
    BLOCK2 DB 0AAH, 0BBH, 0CCH
.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX
    LEA SI, BLOCK1
    LEA DI, BLOCK2
    MOV CX, 5
EXCHANGE:
    MOV AL, [SI]
    MOV BL, [DI]
    MOV [SI], BL
    MOV [DI], AL
    INC SI
    INC DI
    LOOP EXCHANGE

    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```

---

## Experiment 6: Basic File Operations (Create, Write, Close)
**Aim:** To implement basic file operations such as creating a file, writing data into the file, and closing the file using 8086 MASM assembly language.

```assembly
.MODEL SMALL
.STACK 100H
.DATA
    FNAME DB 'TEST.TXT', 0
    MSG DB 'HELLO'
    LEN EQU 5
    HANDLE DW ?
.CODE
MAIN PROC
    MOV AX, @DATA
    MOV DS, AX

    ; Create File
    MOV AH, 3CH
    MOV CX, 0
    LEA DX, FNAME
    INT 21H
    MOV HANDLE, AX

    ; Write to File
    MOV AH, 40H
    MOV BX, HANDLE
    LEA DX, MSG
    MOV CX, LEN
    INT 21H

    ; Close File
    MOV AH, 3EH
    MOV BX, HANDLE
    INT 21H

    ; Exit
    MOV AH, 4CH
    INT 21H
MAIN ENDP
END MAIN
```
