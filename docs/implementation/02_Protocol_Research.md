## Protocols & Their Similarities
So in my last post, I said:

```
So from the Jane Street Blog(https://blog.janestreet.com/protocol-emulator-asic-competition/) they recommend starting with UART, SPI, and I2C.  However, I think it's possible that developers could focus too hard on the implementation of these protocols and pidgeon hole themselves into a design that is non optimal or less reconfigurable.  

For my case I will examine the different protocols to see what they share in terms of digital design. I will also examine the stated inspiration materials:
- PIO state machines
- PRU Cores on the TI Sitara
- TTIHP-SRAM-TEST

I'll do this in the blog doc!
```
So that is what we are doing!

Firstly, let's not focus too much on developing a solution straight away by making SRAMs or creating an instruction list, etc.  Let's break the problem down into the fundamental functions required to make this work. 

So, we have protocols and we have specific idioms that they all share. I won't go into the specific detail behind each protocol and how it works, but instead I will state their speciifc use

### UART

flowchart TB
    subgraph UART["UART"]
        direction TB
        TX["Transmit Register"]
        TX_SHIFT["TX Shift Register"]
        RX_SHIFT["RX Shift Register"]
        RX["Receive Register"]
        CLK["Clock Generator"]

        TX --> TX_SHIFT
        TX_SHIFT -->|Serial TX| TX_OUT["TX Output"]

        RX_IN["RX Input"] -->|Serial RX| RX_SHIFT
        RX_SHIFT --> RX

        CLK -.-> TX_SHIFT
        CLK -.-> RX_SHIFT
    end

### SPI
### I2C
### low-speed USB 
### 10Mbit Ethernet
### HDMI?

