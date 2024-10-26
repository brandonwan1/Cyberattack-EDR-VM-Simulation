# EDR Simulation Extra
## Cracking the Windows password obtained from a LSASS memory dump
In the main writeup, I dumped the LSASS memory of the target Windows machine to a file. To obtain the credentials, I copied the file to another Windows host machine with Python. Then, I proceeded to run the pypykatz script on the dump file, revealing the NTLM hashes of the users. Lastly, I looked up the hash on a rainbow table, revealing the decrypted password. 
![hash-cracked](https://github.com/user-attachments/assets/a209f991-d7eb-449e-b3d8-9776ef5d225e)
