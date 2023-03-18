# Task: Reverse Me (Hard)

This challenge was very similar with the [Reverse Me (Easy) task](https://github.com/kallenosf/CTF_Writeups/blob/main/VU_Cyberthon_2023/Reverse%20Me%20(Easy).md) and can be solved in the same way, even though the easy one was awarded with 50pts while the hard one with 250pts (🤔). Perhaps the biggest difference was that this binary (Task-ReverseME-hard) was statically compiled. We can see that using the `file` command:

![file command!](https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard1.png)

The code from the shared libraries is included within the binary, and that's why it's over 60 times larger than the [Reverse Me (Easy) task](https://github.com/kallenosf/CTF_Writeups/blob/main/VU_Cyberthon_2023/Reverse%20Me%20(Easy).md) binary:

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard2.png" width="500" height="50">

It's an **ELF executable** (Executable and Linkable Format) which means it can be run in Unix-like operatng systems. Another important information is that it's **not stripped**, so we could search for interesting symbols.

When we run it, it asks for login and password information. Our goal is to find them since the flag is in the form of *VU{user,password}*.

After disassembling the binary using `objdump` we can identify the functions 
- `succes` at address `0x401ad5` and 
- `fail` at address `0x401aef` 

which are being called after the check of the credentials, depending their validation result.

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard3.png" width="500" height="50">
<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard4.png" width="700" height="250">

Let's have a more dynamic approach than what we did in [Reverse Me (Easy) task](https://github.com/kallenosf/CTF_Writeups/blob/main/VU_Cyberthon_2023/Reverse%20Me%20(Easy).md). We are going to run the binary using a debugger, `gdb`:
<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard5.png" width="500" height="50">

We set a breakpoint at the main function, we run the program and then we disassemble:
<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard6.png" width="700" height="180">
<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard7.png" width="600" height="300">

We then set two additional breakpoints at each `cmp` (compare) instruction. We continue execution and we are prompted for login and password input. We give two random numbers, 1234 and 4321:

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard8.png" width="400" height="150">

We continue execution and we reach the breakpoint at the first `cmp` instruction which compares registers `$eax` and `$edx`. The `$eax` register holds our input, while the `$edx` register is the expected value. We can inspect both by typing `info registers eax edx`. We see that the expected login value is `2018`.

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard9.png" width="400" height="80">

If we contnue the execution the program will output "Sorry, try again" and exit, because the comparison is false. We can change the value of `$eax` to the expected one (2018) by typing `set $eax=2018`. Or we can just run from the start and give the correct login value.

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard10.png" width="400" height="150">

Next, we can similarly find the password value by inspecting the values of `$eax` and `$edx` at the **second** comparison:

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/TaskReverseME-hard11.png" width="470" height="250">

According to the task description the answer format was like *VU{user,password}*, so the flag is:
**`VU{2018,3158}`**
