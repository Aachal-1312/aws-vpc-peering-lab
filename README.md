# aws-vpc-peering-lab
# AWS VPC Peering Lab

Connected two private VPCs with VPC peering, using a bastion host for SSH access.
Built for the EWB Courses AWS internship (Project 01).

## Architecture
- VPC A (10.10.0.0/16): public subnet 10.10.1.0/24 (bastion), private subnet 10.10.2.0/24 (App-A, 10.10.2.10)
- VPC B (10.20.0.0/16): private subnet 10.20.1.0/24 (App-B, 10.20.1.10), no internet gateway
- Peering connection: ewb-a-b-peer, with routes added on both sides
- Region: ap-south-1 (Mumbai)

## What I built
1. Two VPCs with non-overlapping CIDRs
2. Public and private subnets with explicit route table associations
3. An internet gateway for VPC A only
4. Security groups that allow only the required SSH and ICMP traffic (/32 sources)
5. Three EC2 instances: bastion, App-A and App-B (the app servers have no public IP)
6. Peering request, acceptance, and routes in all three route tables

## Test results
| Test | Result |
|---|---|
| Ping App-A to App-B before peering | 100% packet loss |
| Ping App-A to App-B after peering | 0% packet loss |
| Ping App-B to App-A after peering | 0% packet loss |
| SSH to App-B through the bastion | Success |
| Public IPv4 on App-A and App-B | None |
| ICMP rule removed, then restored | Ping failed, then recovered |

Screenshots are in the `screenshots/` folder.

## What I learned
- Peering needs routes on both sides, because replies need a path back.
- An Active peering connection does not guarantee traffic, since routes and security groups decide that.
- Peering is non-transitive and does not give VPC B internet access through VPC A.
- App-B sees the bastion's private IP as the SSH source when I use ProxyJump.

## Problems I fixed
- Missing Name tags on subnets and route tables
- The bastion received 10.10.1.213 instead of 10.10.1.10, so I updated the SSH rules to match
- The VPC B subnet was not associated with its route table
- The bastion SSH rule had to be refreshed when my public IP changed

## Cleanup
All instances, the peering connection, route tables, security groups, subnets, the internet gateway, the VPCs and the key pair were deleted after testing.
