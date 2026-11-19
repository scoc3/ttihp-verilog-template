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

So, we have protocols and we have specific idioms that they all share. I won't go into the specific detail behind each protocol and how it works, but instead I will state their specific data structure.  Since that is what we are trying to achieve. 

### UART

- Asynchronous
- Idle (logic high (1))
- Start bit (logic low (0)): the start bit signals to the receiver that a new character is coming.
- Data bits: the next five to nine bits, depending on the code set employed, represent the character.
- Parity bit: if a parity bit is used, it would be placed after all of the data bits. The parity bit is a way for the receiving UART to tell if any data has changed during transmission.
- Stop (logic high (1)): the next one or two bits are always in the mark (logic high, i.e., 1) condition and called the stop bit(s). They signal to the receiver that the character is complete. Since the stop bit is logic high (1) and the start bit is logic low (0), there is always at least one guaranteed signal change between characters. If the line is held in the logic low condition for longer than a character time, this is a break condition that can be detected by the UART.

### SPI

- Synchronous (SCLK)
- Select Signal (SS)
- Needs to transmit ...
- and receive serial data aligned with a clk (MOSI and MISO).

---
*So far, we have discovered that with the following digital logic blocks, we can create a UART and SPI protocol emulator.  Note, this is very simple, but we will build on it in a while to create more complex structures:*

1. An Asynchronous/Synchronous DataOut FIFO (connected to clock when we are in SPI mode.)
2. A LUT containing the start bit/stop bit. Or when the select signal is Low on SPI.
 (For future sake, let's call these specific structures per communications protocol a "Protocol Glyph". As they are a symbol that is required when transmitting the data, but arent data bits.)
3. A synchronous DataIn FIFO (for receiving data for SPI)
4. Idle High logic for Uart
5. CRC calc block for the data bits - UART.


---

### I2C

This protocol is a bit harder because it directly results on communications from downstream devices to proceed with transactions.  

so there needs to be some form of logic to check that data is a 1 or 0 on a certain clock cycle. 
The logic also needs to be able to wait until said data is the value we want it to be.

apart from that, its the same as before.

1. The ability to set a clock on a pin out
2. The ability to set some data on a clock out. (idle, stop conditions, etc).
3. The ability to store repeatable transaction structures (glyphs) and output them.


### Low-speed USB
Low speed USB adds 2 further problem cases.  Checking frames instead of a data bit (A sequence of data bits rather than a single bit)

you have further logic built in if a target asks for the same packet again!

---

As you can tell, things are starting to get difficult! The more protocols you add the more tiny little differences there are between protocols.  Therefore its important to focus on the primitives - i.e. what you need to be able to do from a high level. If i were to list them all in a big jumble, then my protocol emulator needs to be able to do the following:


- On the fly reconfiguration of ports: a port needs to be reconfigurable from a clk, data pin, differential pair, steady high/low. 
- Efficiently handle the encoding of glyphs: start bits & frames, stop bits & frames, identifiers.  Why dont you just copy that data for each frame?  Borrowing from texture maps in video games, it doesnt make sense to copy that data for every time you want to use it.  You'd rather store a pointer to those instances of data and instructions on how to run them.
- The ability to output data: 1 clock cycle, for multiple clock cycles, and based on data stored in memory.
- The ability to read data into memory.
- The ability to calculate a CRC On data 

so lets think out loud for a sec

we probs need a fifo for data in, data out
some form of hard logic CRC calc, that will be high during transmission
an address block
a glyph LUT and map - which will show the glyphs before and after data to the orchestrator
the orchestrator - which will basically be like the CPU in which you tell it which parts of the function you would like.

so with the fifo idea you an asynchronously load it, then synchronously deload it using the orchestrator.
The orchestrator will need 2 Fifos and 2 LUTs. 
1 LUT/SRAM to hold the tranmission data which is inserted by operator
1 FIFO to hold the receival data
1 LUT to hold the transmission sequence
1 LUT to hold the glyph map - what data should be transmitted where.
1 Encoder? for encoded data? is that worth it?


how is this different to a CPU? well the hard blocks are different and the orchestrator is actually quite a bit different its not just reads and writes its pulling stuff from LUTs all the time.

