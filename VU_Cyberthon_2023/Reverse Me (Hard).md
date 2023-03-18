# Task: Reverse Me (Easy)

This challenge was very similar with the [Reverse Me (Easy) task](https://github.com/kallenosf/CTF_Writeups/blob/main/VU_Cyberthon_2023/Reverse%20Me%20(Easy).md) and can be solved in the same way, even though the easy one was awarded with 50pts while the hard one with 250pts. Perhaps the biggest dfference was that this binary (Task-ReverseME-hard) was statically compiled. We can see that using the `file` command:

![file command!](https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/Reverse_easy1.png "file command")

The code from the shared libraries is included within the binary, and that's why it's over 60 times larger than the [Reverse Me (Easy) task](https://github.com/kallenosf/CTF_Writeups/blob/main/VU_Cyberthon_2023/Reverse%20Me%20(Easy).md) binary.

It's an **ELF executable** (Executable and Linkable Format) which means it can be run in Unix-like operatng systems. Another important information is that it's **not stripped**, so we could search for interesting symbols.

When we run it, it asks for login and password information. Our goal is to find them since the flag is in the form of *VU{user,password}*.

After disassembling the binary using `objdump` we can identify the functions 
- `succes` at address `0x401ad5` and 
- `fail` at address `0x401aef` 

which are being called after the check of the credentials, depending their validation result.

Let's have a more dynamic approach than what we did in [Reverse Me (Easy) task](https://github.com/kallenosf/CTF_Writeups/blob/main/VU_Cyberthon_2023/Reverse%20Me%20(Easy).md). We are going to run the binary using a debugger, `gdb`.

We set a breakpoint at the main function, we run the program and then we disassemble. 

We then set two additional breakpoints at each `cmp` (compare) instruction. We continue execution and we are prompted for login and password input. We give two random numbers, 1234 and 4321.

We continue execution and we reach the breakpoint at the first `cmp` instruction which compares registers `$eax` and `$edx`. The `$eax` register holds our input, while the `$edx` register is the expected value. We can inspect both by typing `info registers eax edx`. So the expected login value is `2018`. 

If we contnue the execution the program will output "Sorry, try again" and exit, because the comparison is false. We can change the value of `$rax` to the expected one (2018) by typing `set $eax=2018`. Or we can just run from the start and give the correct login value.

Next, we can similarly find the password value by inspecting the values of `$eax` and `$edx` at the second comparison.

According to the task description the answer format was like *VU{user,password}*, so the flag is:
**`VU{2018,3158}`**
