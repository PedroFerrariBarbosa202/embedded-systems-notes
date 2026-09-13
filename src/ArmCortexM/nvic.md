# NVIC
- Nested Vectored Interrupt Controller - is not a peripheral, is build into the processor
- vector table of interrupt handlers are located in beginning of ROM
- ROM offset 0x000.0000: stack pointer
- ROM offset 0x000.0004: reset vector
- to activate an interrupt source, its priority needs to be set on NVIC

- how a context switch in a IRQ works:  
    - 1. push registers into stack (with R0 on top)
    - 2. set PC to the vector tables ISR
    - 3. set IPSR to ISR number
    - 4. set top 24 bits of LR to xFFFFFF (signifies to the processor that an ISR is being executed). The bottom 8 bits signify how to return:
        - 0xE1: return to handler mode MSP - using floating point state
        - 0xE9: return to thread mode MSP - using floating point state
        - 0xED: return to thread mode PSP - using floating point state
        - 0xF1: return to handler mode MSP
        - 0xF9: return to thread mode MSP - MOST USED
        - 0xFD: return to thread mode PSP
        ```
        Bit	            Meaning when 1
        bit 4 (0x10)	Basic frame (no FP state saved). If 0 → extended frame (FP registers were also stacked)
        bit 3 (0x08)	Return to Thread mode. If 0 → return to Handler mode
        bit 2 (0x04)	Use PSP after return. If 0 → use MSP
        ```

- MSP: main pointer stack - the one SP refers to in basic firmware (without RTOS)
- PSP: process stack pointer - used in RTOSs to refer to a specific task's stack pointer
- handler mode always forces MSP usage  
- example of a context switch:
```
IPSR = 0                                IPSR = 18
LR = ???                                LR = 0xFFFFFFF9
 ________                                ________
|  RAM   |                              |  RAM   |       
|        |                              |        |
|        |                              |   R0   | <-- MSP
|        |                              |   R1   |
|        |                              |   R2   |     
|        |                              |   R3   | 
|        |                              |   R12  | 
|        |                              |   LR   | 
|        |                              |   PC   | 
|  base  |   <-- MSP                    |   PSR  | 
|________|                              |________|    
  Stack                                   Stack
  
  BEFORE                                  AFTER
```