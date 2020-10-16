- References
	- https://docs.aws.amazon.com/vpc/latest/userguide/vpc-getting-started.html
	- https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-vpc.html#examples
	- For later: https://docs.aws.amazon.com/directoryservice/latest/admin-guide/gsg_create_vpc.html
- Creating the initial VPC
	- Starting with a fresh blank AWS account
	- There is an existing, default VPC in the 172.31/16 space, with all of its associated subnets, gateways and whatnot.  Going to leave those alone for now as I stand up the new 10/8 VPN/subnets, then I'll go back and nuke all the default junk
	- Naming: awsvpc0_us-east-1
		- First AWS VPC in us-east-1
	- IPv4 CIDR: 10.2.0.0/16
		- Leave 10.0 and 10.1 alone for the huge variety of default stuff that might end up there from containers, VMs, VPNs, etc
```$ aws ec2 create-vpc \
--cidr-block 10.2.0.0/16 \
--amazon-provided-ipv6-cidr-block
{
    "Vpc": {
        "CidrBlock": "10.2.0.0/16",
	[...]
        "IsDefault": false
    }
}
```
- Create some subnets
	- Planning..
		- 10.2.8.0/22 - Server VLAN 1
		- 10.2.12.0/22 - Server VLAN 2
		- 10.2.16.0/22 ...
		- 10.2.20.2/22 ...
	- The first /20 of the subnet are reserved for later, nice to leave lots of space up front and behind for expanding
	- /20 is the largest broadcast domain we'll ever ever want to consider imo, /22 is a good big chunk of space for stuff
```$ aws ec2 create-subnet \
--vpc-id [...] \
--cidr-block 10.2.8.0/22 \
--availability-zone-id use1-az4
