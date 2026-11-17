## The Challenge
Jane street has issued a challenge - Design an open source, general-purpose protocol emulator ASIC.  

When thinking about this challenge, it isnt enought to just quickly bang out a protocol emulator.  A few sub challenges pop out in my mind.  I think its important to establish these sub-challenges before implementing any RTL, verification, etc. These challenges are one I think are interesting to explore - and not exhaustive of the challenges goal!

1. How do you design your protocol emulator to support as many protocols as possible?
2. 1. How do you create your repo/harness in such a way that AI can do as much of the heavy lifting as possible? (I work as much as I can, so having AI do a lot of the manual labour on this task appeals to me.  "Laziness when combined with expertise creates new avenues in work" - me)
2. 2.  How do you make the AI work granular enough, such that AI can verify its own work - it's silly to have an AI generate a full UVM test bench for a whole module, when it could formally prove small modules and assemble components into a whole product.
3. How do you make your design as efficient and small as possible? We have constraints in terms of area here.  CMOS

*Let's add a little more detail around each issue, before diving into them on their own documentation page!*

## Supporting as many protocols as possible?
So from the Jane Street Blog(https://blog.janestreet.com/protocol-emulator-asic-competition/) they recommend starting with UART, SPI, and I2C.  However, I think it's possible that developers could focus too hard on the implementation of these protocols and pidgeon hole themselves into a design that is non optimal or less reconfigurable.  

For my case I will examine the different protocols to see what they share in terms of digital design. I will also examine the stated inspiration materials:
- PIO state machines
- PRU Cores on the TI Sitara
- TTIHP-SRAM-TEST

I'll do this in the blog doc!

## Completing development of RTL and Verification using AI
I have set myself the challenge (whether I am able to do it is a different question) to not write a single line of RTL.  As well as completing the formal verification through an agent harness.  I'll go more into how I plan on doing that in a seperate blog doc, but doing things this way coincides with my overall theory that eventually all of us FPGA engineers will be module specification & digital design engineers!

In the future, I imagine us writing mod specs, writing low-level requirement specs, and then letting AI handle the rest. I envision a harness for use in doing this, but will develop this in detail in a future blog doc!

---
** See you in the next one! **