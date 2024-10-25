# Cyberattack-EDR-VM-Simulation
## Goal
Using a virtual environment, learn about how various cyberattacks are executed, and how security professionals detect and respond to them.
## References
This exercise was sourced from security professional Epic Capuano's guide, "So you want to be a SOC Analyst?": https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-intro?sd=pf
## Summary
Simulating cyberattacks and endpoint detection and response (EDR) using:
1) An Ubuntu attack server
2) The Sliver framework for generating malicious command and control (C2) payloads
3) A vulnerable Windows endpoint for executing the malicious payloads
4) The LimaCharlie SecOps Cloud Platform for conducting EDR for the Windows VM
## Virtual Environment Setup
Windows 11 Endpoint -
- Disabling Tamper Protection and other forms of protection
![06-win-tamper-protect-off](https://github.com/user-attachments/assets/00e69421-8ff3-448d-8efc-60b3c5694096)

- Disabling Windows Defender via the Local Group Policy Editor
![07-disable-ms-defender-av](https://github.com/user-attachments/assets/f5bb4be0-b044-46f2-bffe-4055f1ac9206)

- In safe mode, permanently disabling Defender via the Registry Editor by setting the Start key of several locations to "4": 
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
![16-sensors-lc](https://github.com/user-attachments/assets/4dc00c12-d42d-462a-9d8a-52e6b4fd946b)


Ubuntu VM -
- Install the Sliver adverserial framework 
![11-install-sliver](https://github.com/user-attachments/assets/32e189e7-8fc8-43cd-b2e8-ab0790ddd670)

## Attacking the Windows machine
- Generate the C2 payload via Sliver silver and send it to the Windows machine
![13-gen-send-c2-payload](https://github.com/user-attachments/assets/f39cfe35-f05b-4dab-b2d7-b42fc6b4c6f1)

- Start an HTTP listener via Sliver on the attack machine. The Windows machine will execute the payload, starting the C2 session. The attacker can now gather information on the endpoint such as prvileges, and the security products protecting the system.
![14-c2-started](https://github.com/user-attachments/assets/51bbca4f-6629-4dde-95c6-9e760723513c)
![15-output-pst-cmd](https://github.com/user-attachments/assets/eb08a945-4ffc-4e34-a762-bf7fa9972d86)

## Observe EDR Telemetry in LimaCharlie
Gather information regarding the attack by observing the sensor logs
![20-windows-telemetry](https://github.com/user-attachments/assets/2e86e6be-faaa-4eff-a3f8-c9acec0045d6)
Query the VirusTotal database for the hash of the C2 payload. Since the payload was just created, VirusTotal will not have recognized it.
![19-no-hash-found](https://github.com/user-attachments/assets/1556ff32-35dc-4329-9e88-6ba75be39a8b)


