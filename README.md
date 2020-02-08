# amestris-vpn

This codebase used [marblecodes](https://github.com/marblecodes/openvpn-aws-tf-ansible) OpenVPN solution as a skeleton, however enough modifications have been made to justify another repo. I apologize for not properly forking the repository, I originally did not intend to make this fork public.

The bash wrapper extends the functionality marblecodes wrote to make it a much more user-friendly experience, as well as much faster by automatically retrieving the instance ID and Elastic IP.
### Features

- Configurable port to bypass captive portals
- Forward all traffic, including DNS
- Easily deploy in any AWS region
- Client-to-client subnet with the ability to add multiple clients
- Standalone `.ovpn` files for easy portability on things such as mobile devices

## Installation

#### Dependencies

- AWS Account
- awscli
- ansible-playbook
- terraform

#### Install

```
snap install terraform
snap install aws
sudo apt install ansible
```

[Alternatively, install Terraform manually](https://learn.hashicorp.com/terraform/getting-started/install.html)

Export your aws credentials for the `aws` program to read.

```
export AWS_ACCESS_KEY_ID="YOUR_AWS_KEY_ID"
export AWS_SECRET_ACCESS_KEY="YOUR_AWS_SECRET"
export AWS_DEFAULT_REGION="YOUR_AWS_REGION"
```

`vars.yml` can be further edited to customize VPN options.

## Usage

```
  ->> AMESTRIS <<-
  ->> by stonepresto <<-
 
  USAGE:
   ./amestris [-h|--help]
     -l|--list 				        List indices of regions and their location
     -r|--region <aws_region>		Specify a region by index (--list)
     -p|--port <listen_port>		Port for OpenVPN to use
     -u|--username <username>		Username for OpenVPN client
     -a|--addclient			        Add a client instead of building VPN
     -d|--dryrun			        Do a terraform dryrun instead of applying
     -D|--destroy			        Destroy amestris
```

## Notes

#### Redirection vs. Configured Port

Instead of configuring OpenVPN to listen on a port, `iptables` rules are used to forward traffic from the "outside" port to the default OpenVPN port 1194. This allows for greater flexibility, as otherwise there may be bind errors when OpenVPN attempts to listen on a port that is already in use.

#### Port 53

This port causes a lot of issues thanks to DNS. Recently, Amazon updated their infrastructure in a way that drops malformed DNS packets before it reaches the EC2 instance (or something along those lines), which does not allow the client to connect to OpenVPN.

Prior to the patch, there were other issues, but it still worked. The `/etc/resolv.conf` does not get updated automatically on Ubuntu, so it was necessary to run `update-resolv-conf`, requiring a `sudo apt install resolvconf` and a reboot. This was scripted and still exists in the codebase.

#### Security

I am open-sourcing this in the hopes that people can work together to make this solution more secure. While there are other options such as wireguard, it's nice to have options when it comes to home-rolled VPN solutions, especially as we see companies such as PIA and NordVPN get compromised.

If you find any security vulnerabilities, please open an issue and it will be resolved as soon as possible. If valid, your handle will be added to the "Wall of Fame" because the author is not like, rolling in cash, yo.

# Wall of Fame

| User | Vulnerability Discovery |
| ---- | ----------------------- |