# 🟥 LLMNR-NBT-NS-Poisoning-ATTACK 🟥

This project is a sub-project to my [ESXi-Home-SOC-Lab-Network-Overview](https://github.com/gervguerrero/ESXi-Home-SOC-Lab-Network-Overview).

Here I explore launching a man in the middle LLMNR/NBT-NS Poisoning attack to gain an NTLM hash for an account within the ARK.local domain and successfully crack the hash to gain a password. 

Link-Local Multicast Name Resolution and NBT-NS (NetBIOS Name Service) poisoning are attacks used to exploit weaknesses in name resolution protocols found in Windows machines. These protocols are used to identify hosts in the network when DNS doesn't work.

A victim will attempt to connect to a device, and fail. When it fails an LLMNR/NBT-NS broadcast is sent out asking who knows how to connect to the device.

The attacker will send malicious responses to LLMNR/NBT-NS queries. The attacker sends crafted responses to the target machine, pretending to be the device with the requested name. If the target machine accepts the spoofed response, it may use the attacker's IP address to communicate with the attacker instead of the legitimate one.

This results in the attacker capturing NTLM hashes of the account trying to connect to the requested device. With the hash, the attacker can either crack them, or relay those credentials to another machine in the network for lateral movement and privilege escalation. 

## Network Map
![V2-01222021-CYBER-INTERFACE-HD](https://github.com/gervguerrero/LLMNR-NBT-NS-Poisoning-ATTACK/assets/140366635/73938a13-2c11-4d82-8948-99050ec605ea)

**Note that in this exercise I temporarily had the victim workstation's IP set as 192.168.0.6 instead of 192.168.10.6 seen on the map above.**

## Launching the Attack 

Using a built in Python tool in Kali Linux called Responder, we can set up a listener to respond to incoming LLMNR/NBT-NS requests:

![image](https://github.com/gervguerrero/LLMNR-NBT-NS-Poisoning-ATTACK/assets/140366635/9a122092-889b-42e3-a40c-bab3c0c73bce)

If a victim tries to connect to the Attacker's IP (192.168.10.99) as remote share, it won't know where to go and sends out a broadcast.
![image](https://github.com/gervguerrero/LLMNR-NBT-NS-Poisoning-ATTACK/assets/140366635/6036a56e-7dc8-4fa7-beb3-1c9c6898d49c)

The attacker then receives the broadcast, and tells the victim to connect to the attacker. The victim returns an NTLM username and hash thinking it will authenticate to the correct requested location.

![image](https://github.com/gervguerrero/LLMNR-NBT-NS-Poisoning-ATTACK/assets/140366635/819f4af6-2046-47fd-b002-d62ebb46cb8d)

This is the Hash of the victim account's password. Here is the victim account:

![image](https://github.com/gervguerrero/LLMNR-NBT-NS-Poisoning-ATTACK/assets/140366635/885ebb4a-ae75-4489-b2b6-2574143d33e4)

With the victim's hash, we can crack it using a password list and hashcat, or relay them for lateral movement.

Here is hashcat using a password list to crack the hash:

![image](https://github.com/gervguerrero/LLMNR-NBT-NS-Poisoning-ATTACK/assets/140366635/bbab97af-dbec-4c78-a08e-b39f6b95f7d3)

Here is the password "Password321" that hash been cracked from the hash:

![image](https://github.com/gervguerrero/LLMNR-NBT-NS-Poisoning-ATTACK/assets/140366635/390d09a4-a432-48b4-acce-8caefd94673e)

## Preventing the Attack 

The best defensive approach to prevent LLMNR and NBT-NS poisoning, is a layered defense approach:
1. Disable LLMNR/NBTNS.
2. If it cannot be disabled in the network, implement strong access control with network segregation and subnets. If the attacker is isolated in another network, they cannot perform this attack.
3. Implement stronger, longer, more complex passwords. The more of these characteristics are implemented in password policy, the longer and harder they will be to crack for an attacker. 

🔵 **To view this attack from a Blue Team network defender's perspective, see** [LLMNR-NBT-NS-Poisoning-DETECTION](https://github.com/gervguerrero/LLMNR-NBT-NS-Poisoning-DETECTION). 🔵
