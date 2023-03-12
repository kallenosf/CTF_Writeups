# Task: Reverse Me (Easy)

We have the binary named `Task-ReverseMe`

Firstly, let's try to get some details about the binary using the `file` command:

![file command!](https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/Reverse_easy1.png "file command")

It's an **ELF executable** (Executable and Linkable Format) which means it can be run in Unix-like operatng systems. Another important information is that it's **not stripped**, so we could search for interesting symbols.

We can try run it:

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/Reverse_easy2.png" width="500" height="120">

It seems that it asks for login information. We probably need to find a username and a password to get the flag.

Let's search for through its symbols, using the `readelf` command, since it's not stripped. We filter our results to **FUNC**tion symbols:

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/Reverse_easy3.png" width="700" height="500">

The functions 
- `succes` at address `0x11a9` and 
- `fail` at address `0x11c3` 

are probably the functions that are being called after the check of the credentials.

Let's disassemble the binary using `objdump`. In the main function we observe the following:

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/Reverse_easy4.png" width="700" height="60">

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/Reverse_easy5.png" width="700" height="300">

We can see two `cmp` (compare) instructions and immediately afterwards a `jne` (jump if not equal) for each one of them. If the two compared registers have not the same value, we jump to the `fail` function.
Those two comparisons are probably for username and password.

We can see this more clearly in **Ghidra**:

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/Reverse_easy7.png" width="600" height="500">

- The user input for *login* is saved in *local_20*
- The user input for *password* is saved in *local_1c*

Those values are compared with two local integer variables that are initialized at lines 15 and 16. Therefore, if we input `0x3f1` for the login and `0x62b` for the password we will be able to get the flag:

<img src="https://raw.githubusercontent.com/kallenosf/CTF_Writeups/main/VU_Cyberthon_2023/img/Reverse_easy6.png" width="500" height="170">

**Note: 3f1(hex) = 1009(dec), 62b(hex) = 1579(dec)**

According to the task description the answer format was like *VU{user,password}*, so the flag is:
**`VU{1009,1579}`**
