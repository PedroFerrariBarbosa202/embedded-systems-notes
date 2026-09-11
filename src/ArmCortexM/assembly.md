# Assembly

## Stack
- its a region of memory treated as a data structure
- grows upwards (from higher to lower addresses)
- SP register points to top of register
- allocation:
    ```asm
    sub SP, SP, #40    ;allocates 10 bytes
    add Sp, SP, #40    ;deallocates 10 bytes
    ```
- PUSH and POP: instructions to directly manipulate the stack
    ```asm
    push {R4-R11, LR}   ;push register and return address into the stack
    ; ...
    pop {R4-R11, PC}    ;pop from stack and put data on registers and PC
    ```

### Stack frame
- to better access data from the stack in a function, a register (suck as R11) serves as a stack frame
- stack frame will point to the base of the stack of that specific function
- order of data allocated on stack after a function call:
    - 1. Parameters
    - 2. Return address
    - 3. Saved registers
    - 4. Local Variables
- one advantage of a stack frame is that you can push and pop while still accessing local variables correctly by their symbolic name


### Stack rules
- 1. program segments must have equal number of push and pull operations
- 2. stack acesses should not be performed in unallocated areas
- 3. stack push must first decrement by (4 * BYTES_TO_ALLOCATE)
- 4. stack pop must increment by (4 * BYTES_TO_DEALLOCATE) after reading data

## Parameter passing
- methods to pass parameters
    - call by value: makes a local copy of the data
    - call by reference: passes a pointer to the data
- by AAPCS convention, the first four parameters are passed in the R0-R3 registers  
(if more than 4 parameters: use the stack)
- in assembly: multiple return values can be passed through registers