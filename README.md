# AWS-Day-41-Network-Segmentation-Project
1. Goal
Build a 3-tier VPC with isolated public, app, and database layers.
Enforce least-privilege flows with Security Groups and NACLs.
Prove segmentation with simple, repeatable tests.

2. Topology
Region: us-east-1 (update if different).
VPC: 10.0.0.0/16.
Public subnets (2 AZs): 10.0.1.0/24, 10.0.2.0/24.
Private-app subnets (2 AZs): 10.0.10.0/24, 10.0.20.0/24.
Private-db subnets (2 AZs): 10.0.100.0/24, 10.0.101.0/24.

3. Routing
Public route table: 0.0.0.0/0 → Internet Gateway, associated to public subnets.
Private-app route table: 0.0.0.0/0 → NAT Gateway, associated to app subnets.
Private-db route table: only local, associated to db subnets.

4. Security Groups
Web-Server-SG (public): Inbound 80/443 from 0.0.0.0/0; 22 from admin IP.
App-Server-SG (private-app): Inbound 22 and 8080 from Web-Server-SG.
Database-SG (private-db): Inbound 3306 from App-Server-SG.

5. Network ACLs
Public-NACL (attach to public subnets)
* Inbound: allow 80/443 from 0.0.0.0/0; 22 from admin IP; 1024-65535 from 0.0.0.0/0.
* Outbound: allow 80/443 to 0.0.0.0/0; 1024-65535 to 0.0.0.0/0.
Private-NACL (attach to app subnets)
* Inbound: allow 80/443 from public CIDRs; 1024-65535 from 0.0.0.0/0.
* Outbound: allow 80/443 to 0.0.0.0/0; 3306 to db CIDRs; 1024-65535 to public CIDRs.
Database-NACL (attach to db subnets)
* Inbound: allow 3306 from app CIDRs; 1024-65535 from app CIDRs.
* Outbound: allow 1024-65535 to app CIDRs.

