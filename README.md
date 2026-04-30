# FHRP

Understanding First Hop Redundancy Protocols (FHRP)

In a standard network, a PC is configured with a single Default Gateway (the IP of a router). If that router fails, the PC loses all access to the outside world even if there is another router sitting right next to it.

FHRP solves this by allowing multiple physical routers to share a single Virtual IP (VIP) and Virtual MAC address. The PCs point to this VIP, and if the main router dies, another one takes over the IP automatically. No manual reconfiguration is needed!

The 3 Main Types of FHRP

1. HSRP (Hot Standby Router Protocol)
   
Status: Cisco Proprietary.

How it works: One router is Active (handles traffic) and one is Standby (waits for failure).

Best for: All-Cisco environments. 

2. VRRP (Virtual Router Redundancy Protocol)
   
Status: Open Standard (IEEE).

How it works: Very similar to HSRP, but works across different vendors (like Juniper, HP, and Cisco). It uses the terms Master and Backup.

3. GLBP (Gateway Load Balancing Protocol)
   
Status: Cisco Proprietary.

How it works: Unlike the others, GLBP allows multiple routers to handle traffic simultaneously. It load-balances traffic across up to four routers using a single VIP but different Virtual MACs.

How to Configure FHRP (Using your HSRP Lab Example)
<img width="1447" height="1034" alt="image" src="https://github.com/user-attachments/assets/18932d46-b593-44a6-8f56-cdecbda44e24" />



If you want to ensure your network stays online even if a router fails, follow these steps to set up HSRP (Cisco's version of FHRP).

Step 1: Point your Endpoints to the VIP

Before touching the routers, configure your PCs. Instead of using a physical router's IP, use a Virtual IP (VIP) that doesn't exist yet.

On PC1 & PC2: Set the Default Gateway to 10.0.1.250
<img width="1382" height="1412" alt="Screenshot 2026-03-02 213853" src="https://github.com/user-attachments/assets/6aef845b-7cb6-4128-8ae4-ba0dd4fde822" />
<img width="1380" height="1422" alt="Screenshot 2026-03-02 213903" src="https://github.com/user-attachments/assets/44da9663-55f8-4560-b518-85872e4bf0f4" />


Step 2: Configure the Active Router (The Primary)(R2)

Go to the interface of the router you want to handle traffic. Use a high priority to make sure it wins the "election."

Router(config)# interface g0/0

Router(config-if)# standby version 2

Router(config-if)# standby 9 ip 10.0.1.250        # Assigning the VIP

Router(config-if)# standby 9 priority 200        # Higher priority (Default is 100)

Router(config-if)# standby 9 preempt             # Allows it to "take back" Active status
<img width="1061" height="535" alt="Screenshot 2026-03-02 214152" src="https://github.com/user-attachments/assets/c4c96795-47d4-44fc-b808-8b31d80727f0" />


Step 3: Configure the Standby Router (The Backup)(R1)

On the second router, use the same VIP but a lower priority.

Router(config)# interface g0/0

Router(config-if)# standby version 2
                
Router(config-if)# standby 9 ip 10.0.1.250

Router(config-if)# standby 9 priority 50         # Lower priority means it stays Standby
<img width="981" height="558" alt="Screenshot 2026-03-02 214416" src="https://github.com/user-attachments/assets/9e3f0179-b2f0-46de-b7e2-ea7fdee7b5bf" />


Step 4: Verify with Ping and Tracert

Now, test the connectivity from your PC to an external server.

Ping: Run ping You should see successful replies.

Tracert: Run tracert Even though your PC points to .250, you will see the IP of the Active physical router (e.g., .252) as the first hop.
<img width="1162" height="1305" alt="Screenshot 2026-03-02 214557" src="https://github.com/user-attachments/assets/91a6bfab-0cea-4351-bca2-3596653c36a5" />
<img width="1215" height="1119" alt="Screenshot 2026-03-02 214633" src="https://github.com/user-attachments/assets/a4e74c0e-fd05-4c4d-91b4-217bed38a5c5" />
