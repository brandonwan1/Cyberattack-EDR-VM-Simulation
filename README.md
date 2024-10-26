# Cyberattack-EDR-VM-Simulation
## Goal
Using a virtual environment, learn about how various cyberattacks are executed, and how security professionals detect and respond to them.

## Summary
Simulating cyberattacks and endpoint detection and response (EDR) using:
1) An Ubuntu attack server
2) The Sliver framework for generating malicious command and control (C2) payloads
3) A vulnerable Windows endpoint for executing the malicious payloads
4) The LimaCharlie SecOps Cloud Platform for conducting EDR for the Windows VM
## Virtual Environment Setup
### Windows 11 endpoint (to be attacked)
- Disable Tamper Protection and other forms of protection.
  
   ![06-win-tamper-protect-off - Copy](https://github.com/user-attachments/assets/cda2c6f3-dfee-429c-a959-b2d51339bbda)

- Disable Windows Defender via the Local Group Policy Editor.
  
    ![07-disable-ms-defender-av](https://github.com/user-attachments/assets/4fbdcb7b-66e8-4d7d-bb9c-f66b90f305a5)


- In safe mode, permanently disable Defender via the Registry Editor by setting the Start key of several locations to "4": 

    ![08-reg-key-ch-val - Copy](https://github.com/user-attachments/assets/392ffceb-7bc7-48ce-ad11-cd55641f22fe)

- Keys edited:
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Sense
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdBoot
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WinDefend
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisDrv
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisSvc
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdFilter

- Restart the system in Normal mode.

- Connect the endpoint to the LimaCharlie platform by installing the LimaCharlie sensors

  ![10-installing-lc-sensor - Copy](https://github.com/user-attachments/assets/eee6f8b5-dbfd-4746-acbe-48d600f98f7a)


- Verify that endpoint was successfully connected

  ![16-sensors-lc - Copy](https://github.com/user-attachments/assets/295772d1-c6dc-4588-bf5b-838578c6175f)

### Ubuntu VM (attacker machine)
- Install the Sliver adverserial framework. 

    ![11-install-sliver - Copy](https://github.com/user-attachments/assets/df7b73fd-c9f0-4413-8512-314923fced77)


## Start attacking the Windows machine
- Generate the C2 payload via Sliver silver and send it to the Windows machine.

    ![13-gen-send-c2-payload - Copy](https://github.com/user-attachments/assets/ef57eec3-9653-44ee-914b-4397af98c5e9)


- Start an HTTP listener via Sliver on the attack machine. The Windows machine will execute the payload, starting the C2 session. The attacker can now gather information on the endpoint such as prvileges, and the security products protecting the system.

    ![14-c2-started - Copy](https://github.com/user-attachments/assets/8f2fe35c-a461-46d8-b5ba-6a070379b0ff)

- Output of "ps -T"

    ![15-output-pst-cmd - Copy](https://github.com/user-attachments/assets/db7a5489-e58e-46e9-bc61-a51033db9944)

## Observe EDR Telemetry in LimaCharlie
- Gather information regarding the attack by observing the sensor logs.

    ![20-windows-telemetry - Copy](https://github.com/user-attachments/assets/4a5ee597-ae64-4480-900e-029df4df318a)

- Query the VirusTotal database for the hash of the C2 payload. Since the payload was just created, VirusTotal will not have recognized it.

    ![19-no-hash-found - Copy](https://github.com/user-attachments/assets/a9f60332-2408-4260-aad6-3a21afa329ce)


## Create a detection rule
- While running Sliver on the attack machine, dump the memory contents of the Windows machine's Local Security Authority Subsystem Service (LSASS), a service that stores sensitive information such as logon credentials.

    ![image](https://github.com/user-attachments/assets/2297fac7-4aeb-47fb-8ac3-b84da39172b9)

- This causes an event of type SENSITIVE_PROCESS_ACCESS to show in the logs

    <img width="794" alt="21-sensitive-lc" src="https://github.com/user-attachments/assets/3e95c57a-65e9-48e9-aaa4-ee5c1667e248">

- Create a detection rule for when lsass.exe is accessed. When detected, report the event in the logs

    <img width="445" alt="22-dr-rule" src="https://github.com/user-attachments/assets/85bbc9d4-64b5-4eef-bdf2-1f521dd7c7eb">

- Due to the detection rule, LSASS Access now shows in the logs.

    <img align="center" alt ="lsass-access" src="https://github.com/user-attachments/assets/7115497e-f317-4d79-8572-db0492389986">

## Respond to a threat
- Run a command to delete the volume shadow copies on the Windows system, a tactic used in ransomware to prevent file recovery. Before a D&R rule, only a log is generated. After defining a rule, the parent process (the C2 payload) is terminated. The shell is terminated due to this, stopping the attack.

    ![vss-before-after-rule](https://github.com/user-attachments/assets/144e6611-2e53-476c-9d76-40ebfd9e8a79)

- Log generated from the attack

    ![vss-telemetry](https://github.com/user-attachments/assets/386b8397-6e62-476f-b3e1-c8feba66df06)

## Conclusion
From this exercise, I gained hands-on experience with virtualization, various cyberattacks, and using an EDR solution to detect and respond to them.

## References
This exercise was sourced from security professional Epic Capuano's guide, "So you want to be a SOC Analyst?": https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-intro?sd=pf



