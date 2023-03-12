# Task: Reverse Me (Easy)

We have the binary named `Task-ReverseMe`

Firstly, let's try to get some details about the binary using the `file` command:

It's an **ELF executable** (Executable and Linkable Format) which means it can be run in Unix-like operatng systems. Another important information is that it's **not stripped**, so we could search for interesting symbols.

We can try run it:

It seems that it asks for login information. We probably need to find a username and a password to get the flag.

Let's search for through its symbols, using the `readelf` command, since it's not stripped. We filter our results to **FUNC**tion symbols:

The functions 
- `succes` at address `0x11a9` and 
- `fail` at address `0x11c3` 

are probably the functions that are being called after the check of the credentials.

