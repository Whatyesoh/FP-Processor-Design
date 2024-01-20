# Floating Point Processor Design



### To design this processor, I started with a list of goals/ basic design choices:

1. Floating point operations are a priority
   - Floating point numbers have a lot of uses, especially in AI, so having a simple processor that's geared towards using floating point numbers would open up the possibility for small scale implementations of these technologies.
2. 16 bit word size
   - I wanted 16 bits as it supports the use of floating point numbers while still keeping word size small.
3. Multicycle
   - A multicycle processor would give me better performance and would be compatable with any other design choices I made.
4. Simple instruction set
   - Each instruction has to follow a certain format and needs to be the same length. This gives me less option for code but makes the design much simpler.
5. Register 0 is hard coded into instructions
   - A 16 bit instruction set doesn't give me much room in my instructions for registers or opcodes. To increase the number of opcodes/registers I can use, I have instructions that use two registers use register 0 as one of those registers. This will make these operations slower as data needs to be moved into and out of register 0, but leaves room in the design for more floating point operations and data storage.
6. Bit in instruction dedicated to signed/unsigned and int/floating point
   - This will make instructions much simpler. Signed/unsigned might be a bit excessive, but the distinction between floating point numbers and integers is necessary for almost all instructions.

### Based on these design decisions, I came up with an instruction set that uses two different formats:

1. "I" Format:
   - This format is for using immediates. The processor knows to use this format if the first four bits of the opcode are 0.
   - ``[####------------]``: Opcode
   - ``[----#-----------]``: Integer or floating point
   - ``[-----###########]``: Immediate
2. "O" Format:
   - This format is used for every other operation. The processor will use this if it knows not to use the "I" format.
   - ``[####------------]``: Opcode
   - ``[----#-----------]``: Integer or floating point
   - ``[-----#----------]``: Signed or unsigned
   - ``[------#####-----]``: Destination register (DR)
   - ``[-----------#####]``: Value register (VR)

### A table of instructions I made to fit all these parameters

| Opcode | Instruction | Function | Format | Special Notes |
| --- | --- | --- | --- | --- |
| 0000 | li | Load Immediate | I | |
| 0001 | addi | Add Immediate | I | Always signed |
| 0010 | beq | Branch if Equal | O | Branch to DR if VR equals R0|
| 0011 | blt | Branch if Less Than | O | Branch to DR if VR is less than R0 |
| 0100 | bnq | Branch if not Equal | O | Branch to DR if VR doesn't equal R0|
| 0101 | mv | Move | O | |
| 0110 | ldw | Load Word into R0| O | DR is the base. VR is the offset |
| 0111 | stw | Store Word from R0| O | DR is the base. VR is the offset |
| 1000 | ldb | Load Byte into R0| O | DR is the base. VR is the offset |
| 1001 | stb | Store Byte from R0| O | DR is the base. VR is the offset |
| 1010 | ct | Change Type | O | The selected type is the desired type|
| 1011 | add | Addition | O | Adds VR to R0|
| 1100 | mult | Multiplication | O | Multiplies VR by R0 |
| 1101 | neg | Negation | O | |
| 1110 | jrs | Jump to Register| O | Branch to VR |
| 1111 | jrs | Jump to Register and Save  | O | Save PC to DR and branch to VR|
