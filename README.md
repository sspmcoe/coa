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

**Software Requirements:**
* MASM (Microsoft Macro Assembler)
* DOSBox (DOS emulator)
* DEBUG utility
* Text editor (e.g., Notepad)

**Theory:**
The Arithmetic Logic Unit (ALU) of a microprocessor performs arithmetic operations such as addition, subtraction, multiplication, and division. In 8086 Assembly:
* `ADD` adds two values.
* `SUB` subtracts one value from another.
* `MUL` multiplies unsigned numbers.
* `DIV` divides unsigned numbers.

Registers like `AL`, `AH`, and `AX` are used for these operations, and the results are stored accordingly.

**Instructions Used:**

| Instruction | Operation Performed |
| :--- | :--- |
| `ADD AL, NUM2` | $\text{AL} = \text{AL} + \text{NUM2}$ |
| `SUB AL, NUM2` | $\text{AL} = \text{AL} - \text{NUM2}$ |
| `MUL BL` | $\text{AX} = \text{AL} \times \text{BL}$ |
| `DIV BL` | $\text{AL} = \text{quotient}, \text{AH} = \text{remainder}$ |

**Algorithm:**
1. Define the values of `NUM1` and `NUM2` in the `DATA` segment.
2. Initialize the data segment using `MOV AX, @DATA` and `MOV DS, AX`.
3. Perform:
   * Addition using `ADD`
   * Subtraction using `SUB`
   * Multiplication using `MUL`
   * Division using `DIV`
4. End program using `INT 21H`.

### MASM Code (`ARITH.ASM`)

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

## Experiment 3: Implementation of Number Conversion Techniques (HEX to BCD & ASCII to BCD)

**Aim:** To write and execute 8086 assembly language programs in MASM to perform number system conversions:
1. Hexadecimal to BCD
2. ASCII to BCD

**Theory:**
1. **HEX to BCD Conversion:**
   * HEX (Base $16$) is converted to BCD (Binary Coded Decimal, Base $10$).
   * BCD stores each decimal digit in $4$ bits.
   * Example: $\text{HEX } 1\text{A} = \text{Decimal } 26 = \text{BCD } 0010\ 0110$
2. **ASCII to BCD Conversion:**
   * ASCII codes of digits `'0'` to `'9'` range from `30H` to `39H`.
   * To convert ASCII to BCD, subtract `30H` from the ASCII value.
   * Example: $\text{ASCII } '5' = 35\text{H} \implies 35\text{H} - 30\text{H} = 05\text{H}\ (\text{BCD } 5)$

**Algorithm:**
* **HEX to BCD:**
  1. Load the hex value into a register.
  2. Convert it to decimal using division.
  3. Store hundreds, tens, and units as separate BCD digits.
* **ASCII to BCD:**
  1. Load the ASCII character.
  2. Subtract `30H`.
  3. Store result in BCD format.

### (A) HEX to BCD Code

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

### (B) ASCII to BCD Code

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

**Theory:**
String comparison is performed by comparing corresponding characters of two strings one by one. If all characters match until the terminating symbol (`$`) is encountered, the strings are considered equal. If any character differs, the strings are considered not equal.

**Algorithm:**
1. Start the program.
2. Initialize the data segment.
3. Load the addresses of the two strings into `SI` and `DI`.
4. Compare characters one by one.
5. If any character is different, display `"STRINGS ARE NOT EQUAL"`.
6. If the end of both strings (`$`) is reached without any mismatch, display `"STRINGS ARE EQUAL"`.
7. Exit the program.

### MASM Code

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

**Aim:** To implement Block Exchange operations using `SI` (Source Index) and `DI` (Destination Index) registers in the 8086 microprocessor.

**Theory (Block Exchange):**
Block exchange is the process of swapping the contents of two memory blocks. During this operation, the data in the first block is exchanged with the data in the second block.
* `SI`: Points to the first block.
* `DI`: Points to the second block.
* `AL` and `BL`: Used as temporary registers to store data during exchange.
* `CX`: Stores the number of bytes to be exchanged.

**Algorithm:**
1. Start the program.
2. Initialize the data segment register (`DS`).
3. Load the address of the first block into `SI`.
4. Load the address of the second block into `DI`.
5. Load the number of bytes into `CX`.
6. Store the first block byte in `AL`.
7. Store the second block byte in `BL`.
8. Exchange the contents of both memory locations.
9. Increment `SI` and `DI`.
10. Repeat until all bytes are exchanged.
11. Terminate the program.

### MASM Code

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

**Theory:**
File operations in 8086 assembly language are performed using DOS Interrupt `21H` services. DOS provides different functions for handling files. Each function is selected by loading the required function number into the `AH` register.

The main file operations used in this program are:
1. **Create File (`AH = 3CH`)**
   * Creates a new file with the specified file name.
   * The file name address is passed through the `DX` register.
   * After successful creation, DOS returns a file handle in `AX`.
2. **Write File (`AH = 40H`)**
   * Writes data into an opened file.
   * `BX` contains the file handle.
   * `DX` contains the address of data to be written.
   * `CX` contains the number of bytes to write.
3. **Close File (`AH = 3EH`)**
   * Closes the opened file.
   * `BX` contains the file handle.

**Algorithm:**
1. Initialize the data segment.
2. Load the file name address.
3. Create a new file using DOS interrupt `21H` function `3CH`.
4. Store the file handle returned by DOS.
5. Load the data to be written into the file.
6. Write data using DOS interrupt `21H` function `40H`.
7. Close the file using DOS interrupt `21H` function `3EH`.
8. Terminate the program.

### MASM Code

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
