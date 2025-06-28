# VPC Module

This module is a wrapper around the [terraform-aws-modules/vpc/aws](https://github.com/terraform-aws-modules/terraform-aws-vpc) module, providing a simplified interface for creating AWS VPCs with public and private subnets, internet gateway, NAT gateways, and associated route tables.

## Features

- VPC with customizable CIDR block
- Public and private subnets across multiple availability zones
- Internet Gateway for public subnet internet access
- NAT Gateway(s) for private subnet internet access
- Route tables and associations
- DNS hostnames and support enabled by default

## Usage

### Basic Example

```hcl
module "vpc" {
  source = "../modules/vpc"
  
  name = "my-vpc"
}
```

### Advanced Example

```hcl
module "vpc" {
  source = "../modules/vpc"
  
  name        = "production-vpc"
  cidr_block  = "10.1.0.0/16"
  
  public_subnets  = ["10.1.1.0/24", "10.1.2.0/24", "10.1.3.0/24"]
  private_subnets = ["10.1.101.0/24", "10.1.102.0/24", "10.1.103.0/24"]
  
  enable_nat_gateway = true
  single_nat_gateway = true
  
  tags = {
    Environment = "production"
    Team        = "platform"
  }
}
```

### VPC Without NAT Gateway

```hcl
module "vpc" {
  source = "../modules/vpc"
  
  name               = "dev-vpc"
  enable_nat_gateway = false
  
  tags = {
    Environment = "development"
  }
}
```

## Required Variables

| Variable | Description | Type |
|----------|-------------|------|
| `name` | Name to be used on all resources as identifier | `string` |

## Optional Variables

| Variable | Description | Type | Default |
|----------|-------------|------|---------|
| `cidr_block` | The CIDR block for the VPC | `string` | `"10.0.0.0/16"` |
| `public_subnets` | A list of public subnets inside the VPC | `list(string)` | `["10.0.1.0/24", "10.0.2.0/24"]` |
| `private_subnets` | A list of private subnets inside the VPC | `list(string)` | `["10.0.101.0/24", "10.0.102.0/24"]` |
| `enable_dns_hostnames` | Should be true to enable DNS hostnames in the VPC | `bool` | `true` |
| `enable_dns_support` | Should be true to enable DNS support in the VPC | `bool` | `true` |
| `create_internet_gateway` | Controls if an Internet Gateway is created for public subnets | `bool` | `true` |
| `enable_nat_gateway` | Should be true if you want to provision NAT Gateways for each of your private networks | `bool` | `true` |
| `single_nat_gateway` | Should be true if you want to provision a single shared NAT Gateway across all of your private networks | `bool` | `false` |
| `map_public_ip_on_launch` | Should be false if you do not want to auto-assign public IP on launch | `bool` | `true` |
| `tags` | A map of tags to assign to the resource | `map(string)` | `{}` |

## Outputs

| Output | Description |
|--------|-------------|
| `vpc_id` | The ID of the VPC |
| `vpc_arn` | The ARN of the VPC |
| `vpc_cidr_block` | The CIDR block of the VPC |
| `public_subnet_ids` | List of IDs of public subnets |
| `private_subnet_ids` | List of IDs of private subnets |
| `public_subnet_arns` | List of ARNs of public subnets |
| `private_subnet_arns` | List of ARNs of private subnets |
| `public_subnet_cidr_blocks` | List of CIDR blocks of public subnets |
| `private_subnet_cidr_blocks` | List of CIDR blocks of private subnets |
| `internet_gateway_id` | The ID of the Internet Gateway |
| `internet_gateway_arn` | The ARN of the Internet Gateway |
| `nat_gateway_ids` | List of IDs of the NAT Gateways |
| `nat_public_ips` | List of public Elastic IPs created for AWS NAT Gateway |
| `public_route_table_id` | ID of the public route table |
| `private_route_table_id` | ID of the private route table |
| `availability_zones` | List of availability zones used by subnets |

## Implementation Details

This module uses the [terraform-aws-modules/vpc/aws](https://github.com/terraform-aws-modules/terraform-aws-vpc) module version `~> 5.0` internally, providing:

- Production-ready, well-tested VPC implementation
- Consistent naming and tagging conventions
- Comprehensive feature set with sensible defaults
- Active maintenance and security updates

## Notes

- Subnets are automatically distributed across available availability zones
- When `single_nat_gateway` is `true`, only one NAT Gateway is created and shared by all private subnets
- When `single_nat_gateway` is `false`, one NAT Gateway is created per public subnet for high availability
- Set `enable_nat_gateway` to `false` to save costs in development environments where private subnets don't need internet access