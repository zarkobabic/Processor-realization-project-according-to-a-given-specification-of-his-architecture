# Processor-realization-project


Processor realization project according to to a given specification of his architecture - use of standard combination and sequence modules for processor implementation in logisim enviroment

[This was a project for the subject of computer hardware 2 in the second year of the Faculty of Electrical Engineering, University of Belgrade]

<hr />
<h1>Processor architecture</h1>



In this project, a processor of the following specifications was designed using a logisim environment:


The part of computers that consists of memory and processor is observed.
The memory capacity is 2<sup>17</sup> bytes. The width of the memory word is 2 bytes.
The processor has a single-address instruction format. Data are integers
with and without sign, a two-byte character.
There are six specialized registers in the processor that are in the registry
file. Registers are address register (AR), data register (DR), two index registers
(XR0, XR1) and two base registers (BR0, BR1). There is also a program status word in the processor
PSW, SP stack top pointer, Interrupt Vector Table Pointer (IVTP) register, address
MAR memory register, MDR memory data register, instruction register IR, accumulator A and PC program counter register.

There are addressless instructions in the processor, conditional jump instructions,
unconditional jump instructions and address instructions:


<h3>1) Addressless instructions</h3>

![image](https://user-images.githubusercontent.com/92127059/160308142-cf7b4f68-5372-4e91-b79b-1ab036558a69.png)


<h3>2) Conditional jump instructions</h3>

![image](https://user-images.githubusercontent.com/92127059/160308163-1dea7442-64a1-43ec-8a9c-80954720a6ce.png)

<h3>3) Unconditional jump instructions</h3>

![image](https://user-images.githubusercontent.com/92127059/160308175-3326190f-e5e4-4c24-87f5-593849a98bbf.png)

<h3>4) Address instructions</h3>

![image](https://user-images.githubusercontent.com/92127059/160308208-396e2db9-595f-4b26-9033-c42ac7510d2b.png)

![image](https://user-images.githubusercontent.com/92127059/160308227-eea3917b-b4e1-4ab5-8a8e-bd2687bb1c7f.png)

STRCPY - instruction copies a string of characters whose starting address is in register BR0 in
memory from the address located in the BR1 register.

Addressing methods:

![image](https://user-images.githubusercontent.com/92127059/160308259-8a1ee5ec-203b-4486-991c-c836cebf212c.png)

X - bits not used.
R - bits denoting the base / index register index used. In the case of
base index addressing with bit shift IR16 indicates which base-index pair
the register is used (BR0 and XR0 or BR1 and XR1).
P - bits that represent scroll with character.


<h3>PSW registry format:</h3>

![image](https://user-images.githubusercontent.com/92127059/160308309-edd8aab6-32b4-4492-a6f9-6c76b00cfffb.png)

An inactive PSWSTART bit rate stops CPU operation, while an active value returns
processor in operation.
The stack grows towards lower memory locations, and the SP register indicates the first free one
memory location.

Interrupt requests can be generated by eight peripheral controllers connected to
already implemented block INTERRUPT_INTERFACE_8. At the inputs BTN_INTR7..0 in the block
INTERRUPT_INTERFACE_8 should bring eight buttons that simulate interrupt requests
peripheral controller. The input UEXT2..0 should be brought to the binary value it represents
index of accepted interruption request. A signal that is active in should be brought to the inta input
in case some of the interrupt requests are accepted (signal for loading into the BRU register).
The output of block intr7..0 represents memorized interrupt requests.

These interruptions are called
external masking interrupts because they come from devices outside the processor and may be allowed
or masked because the processor reacts to them or not, depending on whether they are in
the PSWI class of the PSW program status word register finds a value of 1 or 0, respectively.
Assume that the processor only responds to this type of interruption.
Interrupt request handling consists of two groups of steps.

Within the first group of steps, the program counter PC, accumulator A and program status words PSW are stored on the stack. 
Within the second group of steps, the address of the interrupt routines is determined.
Determining the address of the interrupt routine is realized based on the contents of the interrupt routines address table, 
called the Interrupt Vector Table, and the number of entries in the IV table.
Therefore, it is in the process of initializing the entire system in memory, starting from the address to which
indicates the contents of the IVTP register, created IV table with 8 inputs, so that in inputs 7 to 0
find the addresses of the interrupt routines for each of the interrupts coming along lines intr7 to intr0
coming from block INTERRUPT_INTERFACE_8, respectively. Breaks that come after
lines intr7 to intr0 should be sorted by priority where line intr7 has the highest, a
line intr0 lowest priority level. The number of inputs in Table IV should be generated by the processor
based on the position of the line intr7 to intr0 of the highest priority level at which there is a request for
break.

Processor is implemented using blocks
FETCH, ADDR, EXEC, INTR and 
COMMON:
Block with common sequential and combination networks (COMMON block). Block that
contains auxiliary registers, flip-flops and combination modules used in more than
one stage of instruction execution.
To simulate the processor, it is necessary to add the BTN_RST button that generates the rst signal.
The active value of the rst signal returns the processor to its initial state, and writes it to the PC register
value 1000h, in register PSW 8001h, in register SP F000h, in accumulator A 0h and in register
IVTP 0h. The rst signal is used in each implemented block.



<h2>BLOCKS EXPLANATION:</h2>

1) Instruction fetch block (<b>FETCH block</b>). The FETCH block starts with the instructions read phase
if both the FETCH flip-flop and the PSWSTART bit have a value of 1. After
completed reading the instruction, by entering a value of 1 in the flip-flops ADDR or EXEC
the ADDR block or the EXEC block is started, while entering the value 0 in the flip-flop FETCH
stops the FETCH block. The grinst signal is active in case of reading
instruction with undefined operating code or in case of undefined mode
addressing or in the case of an unauthorized combination of the operating code and the addressing method.
Immediately upon activating the grinst signal processor proceed to loading the next instruction.

2) Address generation block and operand retrieval (<b>ADDR block</b>). The ADDR block starts
with forming the operand address and reading the operand if there is a value of 1 in the ADDR flip-flop. 
After completing the formation of the address and retrieving the operand by entering the value 1
in flip-flop EXEC the EXEC block is started and extended with the execution of the execution phase
operations, while entering a value of 0 in the ADDR flip-flop stops the ADDR block.

3) Operation execution block (<b>EXEC block</b>). The EXEC block starts with a operation performing phase
if the flip-flop EXEC has a value of 1. Upon completion
performing the operation entering the value 1 in the flip-flop INTR starts the block INTR and
it is extended with the execution of the interrupt service phase, while by entering the value 0 in
flip-flop EXEC stops the EXEC block.

4) Interrupt service block (<b>INTR block</b>). The INTR block starts with a phase
interrupt service if the INTR flip-flop has a value of 1. Upon completion
interrupt operation by entering the value 1 in the flip-flop FETCH the FETCH block is started
and starts with the phase of reading the next instruction, while entering the value 0 in the flip flop INTR stops the block INTR.

The operating unit of each block is realized by direct connection
switching networks, and each block except the COMMON block have a control unit
realized by microprogramming.
