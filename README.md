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
![06-win-tamper-protect-off](https://github.com/user-attachments/assets/00e69421-8ff3-448d-8efc-60b3c5694096)

- Disable Windows Defender via the Local Group Policy Editor.
![07-disable-ms-defender-av](https://github.com/user-attachments/assets/f5bb4be0-b044-46f2-bffe-4055f1ac9206)

- In safe mode, permanently disable Defender via the Registry Editor by setting the Start key of several locations to "4": 
![08-reg-key-ch-val](https://github.com/user-attachments/assets/d729159c-4c85-40be-a535-3f0bc0575f45)

- Keys edited:
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Sense
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdBoot
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WinDefend
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisDrv
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdNisSvc
    - Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WdFilter

- Restart the system in Normal mode.

- Connect the endpoint to the LimaCharlie platform by installing the LimaCharlie sensors
![10-installing-lc-sensor](https://github.com/user-attachments/assets/1a7d36f1-c254-4283-b3af-53787a660150)
- Verify that endpoint was successfully connected
![16-sensors-lc](https://github.com/user-attachments/assets/4dc00c12-d42d-462a-9d8a-52e6b4fd946b)


### Ubuntu VM (attacker machine)
- Install the Sliver adverserial framework. 
![11-install-sliver](https://github.com/user-attachments/assets/32e189e7-8fc8-43cd-b2e8-ab0790ddd670)

## Start attacking the Windows machine
- Generate the C2 payload via Sliver silver and send it to the Windows machine.
![13-gen-send-c2-payload](https://github.com/user-attachments/assets/f39cfe35-f05b-4dab-b2d7-b42fc6b4c6f1)

- Start an HTTP listener via Sliver on the attack machine. The Windows machine will execute the payload, starting the C2 session. The attacker can now gather information on the endpoint such as prvileges, and the security products protecting the system.
![14-c2-started](https://github.com/user-attachments/assets/51bbca4f-6629-4dde-95c6-9e760723513c)

- Output of "ps -T"

![15-output-pst-cmd](https://github.com/user-attachments/assets/eb08a945-4ffc-4e34-a762-bf7fa9972d86)

## Observe EDR Telemetry in LimaCharlie
- Gather information regarding the attack by observing the sensor logs.
![20-windows-telemetry](https://github.com/user-attachments/assets/2e86e6be-faaa-4eff-a3f8-c9acec0045d6)

- Query the VirusTotal database for the hash of the C2 payload. Since the payload was just created, VirusTotal will not have recognized it.
![19-no-hash-found](https://github.com/user-attachments/assets/1556ff32-35dc-4329-9e88-6ba75be39a8b)

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



