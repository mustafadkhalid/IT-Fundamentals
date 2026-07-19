## Frame (or Stack Frame)
A Frame is the entire isolated block of memory allocated for a single function call. Think of it as a workspace container. When vulnerable_function starts, a frame is created; when the function finishes, that entire container is destroyed (popped off the stack) so the memory can be reused by the next function.

## Local Buffer
A Buffer is a continuous block of memory allocated inside the frame to hold a specific set of data—usually text strings or arrays. For example, in the code char buffer[16];, the compiler reserves exactly 16 bytes inside the frame.

## Saved Frame Pointer (RBP)
RBP stands for Register Base Pointer. On 64-bit systems, this register points to the exact "base" (the starting boundary) of the current stack frame.

Why it's saved: Because the CPU only has one RBP register, when Function A calls Function B, Function B needs to use that register. To avoid losing Function A's memory boundary, the program pushes Function A's base address onto the stack. This is the Saved RBP. When Function B finishes, it reads this value back to perfectly restore Function A's workspace layout.

## Saved Return Address (RIP)
RIP stands for Register Instruction Pointer. This is the absolute most critical pointer for code execution. The RIP register tracks the exact memory address of the next line of code the CPU is supposed to run.

Why it's saved: When a function is called, the CPU needs to know where to go back to once the function finishes. The program saves the address of the "next line of code" onto the stack right behind the Saved RBP. When the function hits a ret (return) assembly instruction, the CPU pops this Saved RIP off the stack and jumps directly back to that code address to continue the program.

### NOTE:

in x64 architecture register are 8 bytes. notice each register took 8 bytes, that’s why allocating each register decrease by 8 bytes.

<img width="1847" height="1018" alt="Untitled" src="https://github.com/user-attachments/assets/60027ca7-a027-46a4-baf6-a6d14199f47e" />
