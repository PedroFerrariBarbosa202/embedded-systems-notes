# Protocols

## UART / USART
- requires 2 lines
    - TX: Transmits data
    - RX: Receives data
- no clock line: both devices have an agreement on the baud rate
- fast communication (but slower than SPI)
- simple and flexible (easy to implement own packet protocol)

- USART: UART but with a clock line (less common)

- elements to configure when using UART:
    - turn on rcc clock for peripheral
    - set flow control
    - set number of databits
    - set baudrate
    - set number of parity bits
    - set stopbits
    - set mode
    - enable interrupts (in the peripheral and in the NVIC)
    - enable peripheral

## I2C
- requires 2 lines
    - SDA: Data line
    - SCL: Clock line
- slower compared to SPI and UART
- slaves are addressed with 7-10 bit words
- 7-bit addressing is the most common

- average packet structure:  
    ```
            [START] [ADDRESS FRAME] [READ/WRITE] [ACK/NACK] [DATA] [ACK/NACK] [STOP]
    bits:     1            7             1            1       8        1        1
    ```

- write packet:
    - address frame: address of slave to write to
    - write/read byte 1: set to 0 (Write)
    - ACK from slave
    - data byte 1: register to write to
    - ACK from slave
    - data byte 2: info to write to register

- read packet:
    - address frame: addres of slave to read from
    - write/read byte: set to 0 (Write)
    - ACK from slave
    - data byte 1: register to read from
    - ACK from slave
    - repeated start: master doent let go of the bus after first packet
    - write/read byte 2: set to 1 (Read)
    - ACK from slave
    - data byte 2: data received from slave
    - if master sends ACK: slave may transmit another byte (slave increment internal register pointer)


