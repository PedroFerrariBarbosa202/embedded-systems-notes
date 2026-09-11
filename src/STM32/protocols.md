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

## SPI
- requires 4 lines (+1 for each addicional slave)  
(these can have different names)
    - Cs: Chip select
    - Sclk: Clock line
    - Mosi: Master out, slave in
    - Miso: Master in, slave out
- fast protocol
- SPI modes:
    - clock polarity:
        - Active high: clock is idle low and pulled high
        - Active low: clock is idle high and pulled low
    - clock phase
        - clock phase 0: data is sampled in the leading edge
        - clock phase 1: data is sampled in the trailing edge
- multi slave communication:
    - independend slaves: one Cs line is connected to each slave (uses more resources)
    - cooperative slaves/ Daisy chain: slaves pass the data from one to another, until it reaches the correct slave  
    (ones MOSI line is connected to the others MISO)

```
 ________     CS     ________
|        |----------|        |      /* Pulled down/high to initiate communication */            
|        |   SCLK   |        |
|        |----------|        |      /* Clock (Master ALWAYS generates clock) */  
| Master |   MOSI   | Slave  |
|        |----------|        |      /* Master line to send data to slave */
|        |   MISO   |        | 
|________|----------|________|      /* Slave line to send data to master */
```



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


