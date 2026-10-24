# SEIR-Foundations-homework-module1-lab0_basics
# Week 1 Lab – Launching 3 EC2 Instances

## Evidence Table

| # | Name | Instance ID | Public IPv4 | Private IPv4 | Type | AZ |
|---|------|-------------|-------------|--------------|------|----|
| A | wk1-lab-ec2-a | i-05bb1bad0667b1586 | 52.201.244.240 | 172.31.27.156 | t3.micro | us-east-1b |
| B | wk1-lab-ec2-b | i-0c9d474769509075b | 107.21.151.175 | 172.31.21.140 | t3.micro | us-east-1b |
| C | wk1-lab-ec2-c | i-050e1d782fc922d30 | 34.229.91.144 | 172.31.18.124 | t3.micro | us-east-1b |

## The Deployments

**A:** Launched from my launch template (Amazon Linux 2023, t3.micro). Connected with EC2 Instance Connect as `ec2-user` and the SEIR Foundations dashboard popped up right away. 4 minutes uptime, 0% CPU, 179Mi memory used.

**B:** Same template, same steps. Connected over SSH with Instance Connect and the dashboard loaded fine. 7 minutes uptime, 2% CPU, 174Mi memory used.

**C:** Same deal. Launched, connected, dashboard showed up. 3 minutes uptime, 0% CPU, 186Mi memory used.

## What the Dashboard Tells Me

- **CPU (0–2%):** Basically idle... 
- **Memory (~180Mi of 913Mi):** Lots of room left.
- **Disk (1.7G of 8G, 21%):** Plenty of space in the EBS volume.
- **Uptime (3–7 min):** The instances weren't up long.

## What Got Easier

Repeating the steps. After the first one, the launch, connect and check routine was quick and I didn't have to think about it. The launch template did most of the work.

## Errors

None. All three launched and connected on the first try.

## Cleanup

- Launch template kept so I can reuse it.
- All three practice instances terminated.
- Steps added to my notes for next time.
