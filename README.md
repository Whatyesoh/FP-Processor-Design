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
| 0001 | addi | Add Immediate | I | Adds immediate to R0 and stores in R0 |
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
| 1110 | jrs | Jump to Register| O | Branch to DR |
| 1111 | jrs | Jump to Register and Save  | O | Save PC to VR and branch to DR|

### Diagrams showing a high level design of the processor datapath and control unit FSM

![image](https://github.com/Whatyesoh/FP-Processor-Design/assets/43829957/03c4c084-83e9-4173-9d25-7232124d29c7)

![image](https://github.com/Whatyesoh/FP-Processor-Design/assets/43829957/d77b499a-99ed-4c5b-b41b-43ddfbf920e0)

### Reflection on design

1. GPUs already excell at doing efficient floating point arithmetic; however, this works as a standalone and smaller scale computer.
2. The int/floating point and signed/unsigned bits are wasteful
   - They could each be replaced by an extra bit of opcode which would quadruple the number of instructions I can use. This would still let me have signed/unsigned or int/floating point distinctions for the instructions that need it while leaving a lot of extra room for more instructions. I just went with the much less complex option of just having less instructions, though.
3. Restricted to operating on R0
   - The restriction on having to use R0 for a lot of instructions will have an impact on performance as it will take more instructions to execute simple operations. However, I chose to use this approach as it provides the user with many more registers. With my 16 bit architecture, I give the user the same amount of registers as MIPS does with a 32 bit architecture. This isn't a total loss in performance, though, as having more registers means the user doesn't have to rely nearly as much on loads and stores which take a large amount of time. Testing would be required to determine which of these options yield a better performance, but for now, I decided that I prefer having more registers.
4. Datapath/control unit complexity
   - I tried making the datapath less complex and as a result the control unit needs to be more complex to accomodate it. This decision just came down to how much effort it took to make either diagram. The datapath was getting crowded, so I decided to put more logic in the control unit.

### Future work

1. Simulating the processor
   - My next step will be to simulate the processor using a hardware description language such as verilog so I can test my design. I can't actually test if diagrams are accurate yet, I just had to manually check each instruction to see if they worked.
2. Making improvements to the design
   - I would definitely like to add more instructions as mentioned in part 2 of my reflection to improve the performance of my processor. I would also like to make any more design changes that I could think of once I can test and use the processor.
3. Test performance
   - Once I have a working simulation, I could test the performance of my processor and compare it to other processors. I would mostly try to compare the performance relative to other 16 bit processors or to other processors' ability to perform floating point arithmetic. This could give me a good idea of how well my processor performs already and seeing how other processors perform could give me some inspiration on new improvements to add.
 


