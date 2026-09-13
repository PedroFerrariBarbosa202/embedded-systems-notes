# Bootloader
- first program to run (stays on the beginning of DRAM)
- jumps to main firmware after finishes working
- occupies a fixed space in memory (sometimes needs padding)

```Python
    # create a padding for the bootloader.bin so it is exactly 32Kb

    BOOTLOADER_SIZE = 0x8000
    BOOTLOADER_FILE = "bootloader.bin"

    with open(BOOTLOADER_FILE, "rb") as f:
        raw_file = f.read()

    bytes_to_pad = BOOTLOADER_SIZE - len(raw_file)
    padding = bytes([0xff for _ in range(bytes_to_pad)])

    with open(BOOTLOADER_FILE, "wb") as f:
        f.write(raw_file + padding)
```
- interesting uses:
    - add header to firmware
    - upload and update firmware using UART
    - implement custom communication protocol to receive firmware data

## Basic implementation
```C
static void jump_to_main(void){  
  // to circunvent ugly function pointer syntax :)
  typedef void(*void_fn)(void);

  // +4 -> reset handler is in the second address of vector table
  uint32_t *reset_vector_addr = (uint32_t *)(MAIN_APP_START_ADDR + 4);

  // treat the entry in the above address as an address to get the handler address
  uint32_t *reset_vector = (uint32_t *)(*reset_vector_addr);

  // convert above address to function pointer and jump to it
  void_fn jump_fn = (void_fn)reset_vector;

  jump_fn();
}

int main(void) {
  jump_to_main();
  
  // never return
  return 0;
}

```
- when jumping to firmware: it must set its vector table register to point correctly to its own VTable

```C
static void vector_setup(void){
  /* 
  SCB_VTOR -> register that offsets the vector table
  we need to change it so the program uses its own vector table
  instead of the bootloader's one

  therefore, we offset by bootloader size 
  */

  SCB_VTOR = MAIN_APP_START_ADDR;
}
```