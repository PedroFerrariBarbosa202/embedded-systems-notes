# Functional Debugging 

## Dump into Array without filtering

- embedded system may not always have the capability of using print statements for some reasons:
    - 1) bandwidth of the print function may not be large enough
    - 2) system may not have a display available for the output
- solution: add a function/debugger instrument to throw into an array debugging info, retrieving it after the runtime

```asm
SIZE equ 20
ABuf space SIZE
BBuf space SIZE
Cnt  space 4

Save    push {R0-R4, LR}
        ldr R0, =Cnt                        ;R0 = &Cnt
        cmp R1, #SIZE                       ;R1 = Cnt
        bhs done
        ldr R3, =GPIO_PORTA_DATA_R
        ldr R3, [R3]                        ;R3 is Port A
        ldr r2, =ABuf
        strb R3, [R2, R1]                   ;Save Port A
        ldr R3, =GPIO_PORTB_DATA_R
        ldrb R3, [R3]                       ;R3 is Port B
        ldr R3, =BBuf
        strb R3, [R2, R1]                   ;Save Port B
        add R1, #1
        str R1, [R0]                        ;Save Cnt
done    pop {R0-R4, PC}
```

## Dump into Array with Filtering

- problem with dumping: it generates a lot of information
- filter is a condition to add items to the debugging array

```asm
SIZE equ 20
ABuf space SIZE
BBuf space SIZE
Cnt  space 4

Save    push {R0-R4, LR}
        ldr R3, =GPIO_PORTB_DATA_R
        ldr R3, [R3]                        ;R3 is Port B
        cmp R3, #100                        ;checks if Port B less than 100
        bls done                            ;assuming unsigned
        ldr R0, =Cnt                        ;R0 = &Cnt
        cmp R1, #SIZE                       ;R1 = Cnt
        bhs done
        ldr R3, =GPIO_PORTA_DATA_R
        ldr R3, [R3]                        ;R3 is Port A
        ldr r2, =ABuf
        strb R3, [R2, R1]                   ;Save Port A
        ldr R3, =GPIO_PORTB_DATA_R
        ldrb R3, [R3]                       ;R3 is Port B
        ldr R3, =BBuf
        strb R3, [R2, R1]                   ;Save Port B
        add R1, #1
        str R1, [R0]                        ;Save Cnt
done    pop {R0-R4, PC}
```