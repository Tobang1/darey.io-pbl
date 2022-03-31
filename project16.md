# Automate Infrastructure With IAC Using Terraform Part 1

## Building infrastructure for 2 website manualy using terraform

## Getting Started

```
First i created IAM user in  AWS progmatic access and used the awscli to have authentication
installed terraform from teraform website.
installed docker
configuration and did AWS configure to configure my AWSCLI
Installed Python 3 and boto3 on my local machine
Create S3 bucket in AWS s3 console (set a dynamic name for the s3)
Run a python script to import boto and see our s3 bucket with the bellow code

import boto3
s3 = boto3.resource('s3')
for bucket in s3.buckets.all():
    print(bucket.name)

```

### VPC| SUBNET | Security on Local drive

```
Creating a Directory Structure
Created a directoty called Terraform (mkdir Terraform && cd into Terraform)
created a file named main.tf
```

### Provider and VPC resource section

```
adding aws as a provider and a resource to create a vpc in the main.tf file
Provider block informs Terraform that we intend to build infrastructure within AWS.
Resource block will create a VPC.

provider "aws" {
  region = "eu-central-1"
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = "172.16.0.0/16"
  enable_dns_support             = "true"
  enable_dns_hostnames           = "true"
  enable_classiclink             = "false"
  enable_classiclink_dns_support = "false"
}

next I initialize the resource code using terraform init which created a new directory calle .terraform\.... which keeps terraform plugins.
Then ran terraform apply and changes was okay.

While  applying a file was created terraform.tfstate. (used to keep itself uptodate with exact state of the infrastructure)
```

### Subnets resource section

```
This require 6 subnets
2 public
2 private for webserver
2 private for data layer

Added this code below to the main.tf file

# Create public subnets1
    resource "aws_subnet" "public1" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.0.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "eu-central-1a"

}

# Create public subnet2
    resource "aws_subnet" "public2" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.1.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "eu-central-1b"
}

Run terraform plan and terraform apply
NB. Do not destroy an infrastructire that has been deployed to production but run terraform destroy

```

### Fixing THe Problems By Code Refractoring

```
Adding a variable to it in the main.tf

 variable "region" {
        default = "eu-central-1"
    }

    provider "aws" {
        region = var.region
    }


  Doing the same for cidr value in the vpc block and all the arguments

    variable "region" {
        default = "eu-central-1"
    }

    variable "vpc_cidr" {
        default = "172.16.0.0/16"
    }

    variable "enable_dns_support" {
        default = "true"
    }

    variable "enable_dns_hostnames" {
        default ="true"
    }

    variable "enable_classiclink" {
        default = "false"
    }

    variable "enable_classiclink_dns_support" {
        default = "false"
    }

    provider "aws" {
    region = var.region
    }

    # Create VPC
    resource "aws_vpc" "main" {
    cidr_block                     = var.vpc_cidr
    enable_dns_support             = var.enable_dns_support
    enable_dns_hostnames           = var.enable_dns_support
    enable_classiclink             = var.enable_classiclink
    enable_classiclink_dns_support = var.enable_classiclink

    }

I run terraform plan and terraform apply which work fine


```
#### Fixing Multiple resourc block with Loops and Data sources concept.
###### Fetching Availability zone from AWS

```
 # Get list of availability zones
        data "aws_availability_zones" "available" {
        state = "available"
        }
```

###### Next is introducing count argument in the subnet block

```
   # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = "172.16.1.0/24"
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]
    }

NB. The count argument tell us how many resource we need, for this we need 2 subnets which are like this

  ["eu-central-1a", "eu-central-1b"]

  The count foows an index patter from 0 to 1 = 0,1,2,3, ....
```

###### Making Cidr_block dynamic

```
Making a function for cidr cidrsubnet

 # Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = 2
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }

Its parameters are cidrsubnet(prefix, newbits, netnum)

The prefix parameter must be given in CIDR notation, same as for VPC.
The newbits parameter is the number of additional bits with which to extend the prefix. For example, if given a prefix ending with /16 and a newbits value of 4, the resulting subnet address will have length /20
The netnum parameter is a whole number that can be represented as a binary integer with no more than newbits binary digits, which will be used to populate the additional bits added to the prefix



Experimenting using terraform console for functions


On the terminal, run terraform console

type cidrsubnet("172.16.0.0/16", 4, 0)

Hit enter
See the output
Keep change the numbers and see what happens.
To get out of the console, type exit

```
##### Removing the hardcoded count value

```
I introduced length() function which determines the length of a given list, map or string
Since our aws AZ returns a list like ["eu-central-1a", "eu-central-1b", "eu-central-1c"] 
we will use length function to get the number of the AZ

length(["eu-central-1a", "eu-central-1b", "eu-central-1c"])

you can try the above length function in terraform console which you will get an output of 3

```
###### Updating the Public subnet block 

```
# Create public subnet1
    resource "aws_subnet" "public" { 
        count                   = length(data.aws_availability_zones.available.names)
        vpc_id                  = aws_vpc.main.id
        cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
        map_public_ip_on_launch = true
        availability_zone       = data.aws_availability_zones.available.names[count.index]

    }


    Observations:

What we have now, is sufficient to create the subnet resource required. But if you observe, it is not satisfying our business requirement of just 2 subnets. The length function will return number 3 to the count argument, but what we actually need is 2.

so lets fix the nuber of the publick subnet using variable default 



variable "preferred_number_of_public_subnets" {
      default = 2
}

```
###### Update the count argument with a condition 

```
# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]

}



The first part var.preferred_number_of_public_subnets == null checks if the value of the variable is set to null or has some value defined.
The second part ? and length(data.aws_availability_zones.available.names) means, if the first part is true, then use this. In other words, if preferred number of public subnets is null (Or not known) then set the value to the data returned by lenght function.
The third part : and  var.preferred_number_of_public_subnets means, if the first condition is false, i.e preferred number of public subnets is not null then set the value to whatever is definied in var.preferred_number_of_public_subnets

```
##### Declaring Variable.tf

``` 
Normally our main.tf configuration should look like this but we need to put the variable in a seperate file called variable.tf


# Get list of availability zones
data "aws_availability_zones" "available" {
state = "available"
}

variable "region" {
      default = "eu-central-1"
}

variable "vpc_cidr" {
    default = "172.16.0.0/16"
}

variable "enable_dns_support" {
    default = "true"
}

variable "enable_dns_hostnames" {
    default ="true" 
}

variable "enable_classiclink" {
    default = "false"
}

variable "enable_classiclink_dns_support" {
    default = "false"
}

  variable "preferred_number_of_public_subnets" {
      default = 2
}

provider "aws" {
  region = var.region
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = var.vpc_cidr
  enable_dns_support             = var.enable_dns_support 
  enable_dns_hostnames           = var.enable_dns_support
  enable_classiclink             = var.enable_classiclink
  enable_classiclink_dns_support = var.enable_classiclink

}


# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]

}

```
###### Introducing variables.tf & terraform.tfvars

```
Create a new file called variable.tf and terraform.tfvars
Variable.tf = File where all variable goes into 
terraform.tfvars = 
```

###### Main.tf
```
# Get list of availability zones
data "aws_availability_zones" "available" {
state = "available"
}

provider "aws" {
  region = var.region
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = var.vpc_cidr
  enable_dns_support             = var.enable_dns_support 
  enable_dns_hostnames           = var.enable_dns_support
  enable_classiclink             = var.enable_classiclink
  enable_classiclink_dns_support = var.enable_classiclink

}

# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]
}

```
###### Variables.tf
```
variable "region" {
      default = "eu-central-1"
}

variable "vpc_cidr" {
    default = "172.16.0.0/16"
}

variable "enable_dns_support" {
    default = "true"
}

variable "enable_dns_hostnames" {
    default ="true" 
}

variable "enable_classiclink" {
    default = "false"
}

variable "enable_classiclink_dns_support" {
    default = "false"
}

  variable "preferred_number_of_public_subnets" {
      default = null
}

```
###### Terraform.tfvars

```
region = "eu-central-1"

vpc_cidr = "172.16.0.0/16" 

enable_dns_support = "true" 

enable_dns_hostnames = "true"  

enable_classiclink = "false" 

enable_classiclink_dns_support = "false" 

preferred_number_of_public_subnets = 2

```
###### Terraform plan and terraform apply  




