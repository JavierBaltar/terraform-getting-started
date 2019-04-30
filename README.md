# terraform-getting-started
<p align="center">
  <a href="#Getting-Started">Getting Started</a> •
  <a href="#Installing">Installing</a> •
  <a href="#Deploying-Resources">Deploying Resources</a> •
  <a href="#Destroying-Resources">Destroying Resources</a> •
  <a href="#related">Related</a> •
  <a href="#Authors">Authors</a>
</p>

## Getting Started
To deploy AWS resources with Terraform, you need to configure the following items:
- AWS Credentials: set the AWS credentials for your IAM user as the environment variables AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY:
```bash
export AWS_ACCESS_KEY_ID=(your access key id)
export AWS_SECRET_ACCESS_KEY=(your secret access key)
```
> In addition to environment variables, Terraform supports the same authentication mechanisms as all AWS CLI and SDK tools. Therefore, it will also be able to use credentials in ```~/.aws/credentials```

## Installing

You can download Terraform from the Terraform homepage
* [Terraform](https://www.terraform.io) - Terraform Download

Click the download link, select the appropriate package for your operating system, download the zip archive, and unzip it into the directory where you want Terraform to be installed. 
Run the terraform command, and you should see the usage options:

```bash
terraform
Usage: terraform [-version] [-help] <command> [args]

The available commands for execution are listed below.
The most common, useful commands are shown first, followed by
less common or more advanced commands. If you're just getting
started with Terraform, stick with the common commands. For the
other commands, please read the help and docs before usage.

Common commands:
    apply              Builds or changes infrastructure
    console            Interactive console for Terraform interpolations
    destroy            Destroy Terraform-managed infrastructure
    env                Workspace management
    fmt                Rewrites config files to canonical format
    get                Download and install modules for the configuration
    graph              Create a visual graph of Terraform resources
    import             Import existing infrastructure into Terraform
    init               Initialize a Terraform working directory
    output             Read an output from a state file
    plan               Generate and show an execution plan
    providers          Prints a tree of the providers used in the configuration
    push               Upload this Terraform module to Atlas to run
    refresh            Update local state file against real resources
    show               Inspect Terraform state or plan
    taint              Manually mark a resource for recreation
    untaint            Manually unmark a resource as tainted
    validate           Validates the Terraform files
    version            Prints the Terraform version
    workspace          Workspace management

All other commands:
    debug              Debug output management (experimental)
    force-unlock       Manually unlock the terraform state
    state              Advanced state management
```

## Deploying Resources

Terraform code is written in the HashiCorp Configuration Language (HCL), files with the extension .tf. 
As an example, the following bash script runs a tool called busybox to host a web server listening on port 8080
```bash
#!/bin/bash
echo "This webServer is deployed using Terraform!" > index.html
nohup busybox httpd -f -p 8080 &
```

Terraform deployment .rt file

```terraform
resource "aws_instance" "example" {
  ami = "ami-08d658f84a6d84a80"
  instance_type = "t2.micro"

  user_data = <<-EOF
              #!/bin/bash
              echo "This webServer is deployed using Terraform!" > index.html
              nohup busybox httpd -f -p 8080 &
              EOF

  tags {
    Name = "terraform-webserver"
  }
}

```

By default, AWS does not allow any incoming or outgoing traffic from an EC2 Instance. In order to allow the EC2 Instance to receive requests on port 8080, you need to create a security group:

```terraform
resource "aws_security_group" "instance" {
  name = "sg-terraform-webserver"
  ingress {
    from_port = 8080
    to_port = 8080
    protocol = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

Additionally, the output section will provide the public IP assigned to the EC2 instance`

```terraform
output "webserver_public_ip" {
  value = "${aws_instance.example.public_ip}"
}

```
The ```terraform apply``` command applies the configuration changes

```bash
terraform apply

aws_security_group.instance: Refreshing state... (ID: sg-db63663du3)
aws_instance.example: Refreshing state... (ID: i-618363030)

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

webserver_public_ip = 54.176.21.6
```

Finally, test the server deployment ```curl http://webServerPublicIP:8080```

## Destroying Resources

Terraform destroy is a command that allows you to destroy either a full stack or single resources, using the ```-target option```

```bash
terraform destroy -target RESOURCE_TYPE.NAME -target RESOURCE_TYPE2.NAME
```

## Related
* [Terraform](https://www.terraform.io/docs/index.html) - Terraform Documentation
 
## Authors
* **Javier Baltar** - *Initial work* - [GitHub](https://github.com/JavierBaltar)

