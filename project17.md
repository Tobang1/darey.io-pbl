# Automate Infrastructure With IaC using Terraform. Part 2

```
I used Eli the Computer Guy videos on youtube to leatn networking, 
Introduction to Networking
TCP/IP and Subnet Masking
also read about Networking Terminology, Interfaces, Protocols, IP Address, Subnets, and CIDR Notation by Justin Ellingwood from Digital Ocean
```

###### Networking
```
Creating  4 private subnets and keeping in mind
1. Make sure you use variables or length() function to determine the number of AZs
2. Use variables and cidrsubnet() function to allocate vpc_cidr for subnets
3. Keep variables and resources in separate files for better code structure and readability
4. Tags all the resources you have created so far. Explore how to use format() and count functions to automatically tag subnets with its respective number.
```
###### Tagging 
```

Tagging is a straightforward, but a very powerful concept that helps you manage your resources much more efficiently:

Resources are much better organized in 'virtual' groups
They can be easily filtered and searched from console or programmatically
Billing team can easily generate reports and determine how much each part of infrastructure costs how much (by department, by type, by environment, etc.)
You can easily determine resources that are not being used and take actions accordingly
If there are different teams in the organisation using the same account, tagging can help differentiate who owns which resources.

Note:  You can add multiple tags as a default set, for example:

tags = {
  Enviroment      = "production" 
  Owner-Email     = "infradev-segun@darey.io"
  Managed-By      = "Terraform"
  Billing-Account = "1234567890"
}




OR

Now you can tag all you resources using the format below

tags = merge(
    var.tags,
    {
      Name = "Name of the resource"
    },
  )

  reason for tagging is if you make changes to tags, you will only need to change it in one place.

  ```

  ##### Internet Gateway & format() function
  ###### Create an internet Gateway in a seperate Terraform file internet_gateway.tf

  ```
  resource "aws_internet_gateway" "ig" {
  vpc_id = aws_vpc.main.id

  tags = merge(
    var.tags,
    {
      Name = format("%s-%s!", aws_vpc.main.id,"IG")
    } 
  )
}
```

###### Nat Gateways
Creating one Nat Gateway and 1 Elastic EIP addresss 
```
create a file natgateway.tf and input this code


resource "aws_eip" "nat_eip" {
  vpc        = true
  depends_on = [aws_internet_gateway.ig]

  tags = merge(
    var.tags,
    {
      Name = format("%s-EIP", var.name)
    },
  )
}


resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat_eip.id
  subnet_id     = element(aws_subnet.public.*.id, 0)
  depends_on    = [aws_internet_gateway.ig]

  tags = merge(
    var.tags,
    {
      Name = format("%s-Nat", var.name)
    },
  )
}
```

###### AWS routes

create a file route_tables.tf using it to create route for both public and private subnets and the following

aws_route_table
aws_route
aws_route_table_association


```
# create private route table
resource "aws_route_table" "private-rtb" {
  vpc_id = aws_vpc.main.id

  tags = merge(
    var.tags,
    {
      Name = format("%s-Private-Route-Table", var.name)
    },
  )
}


# associate all private subnets to the private route table
resource "aws_route_table_association" "private-subnets-assoc" {
  count          = length(aws_subnet.private[*].id)
  subnet_id      = element(aws_subnet.private[*].id, count.index)
  route_table_id = aws_route_table.private-rtb.id
}



# create route table for the public subnets
resource "aws_route_table" "public-rtb" {
  vpc_id = aws_vpc.main.id

  tags = merge(
    var.tags,
    {
      Name = format("%s-Public-Route-Table", var.name)
    },
  )
}

# create route for the public route table and attach the internet gateway
resource "aws_route" "public-rtb-route" {
  route_table_id         = aws_route_table.public-rtb.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = aws_internet_gateway.ig.id
}

# associate all public subnets to the public route table
resource "aws_route_table_association" "public-subnets-assoc" {
  count          = length(aws_subnet.public[*].id)
  subnet_id      = element(aws_subnet.public[*].id, count.index)
  route_table_id = aws_route_table.public-rtb.id
}
```


