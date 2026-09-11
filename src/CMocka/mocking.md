# Mocking

## Mock functions
- when testing a function: if it has dependencies on others, these internal functions must be mocked
    - example: when testing a I2C API, HAL functions must be mocked so the test doent need to touch actual hardware
- mockup syntax:
    ```C
    /* Original function: */
    int i2c_write(i2c_port_t port, i2c_slave_adr_t adr, uint8_t *data, uint16_t len)

    /* Mockup: */
    int __wrap_i2c_write(i2c_port_t port, i2c_slave_adr_t adr, uint8_t *data, uint16_t len)

    /* The original function will be available with the symbol __real_i2c_write(...) */
    ```

## Linker trick
- it is necessary to indicate in build time what functions will be wrapped
- Makes so the linker substitutes the previous calls to the function with the wrapper symbol

```
-Wl,--wrap=[FUNCTION SYMBOL]
```