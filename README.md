# terraform-getting-started
<p align="center">
  <a href="#Getting-Started">Getting Started</a> •
  <a href="#Installing">Installing</a> •
  <a href="#Deploying-Resources">Deploying Resources</a> •
  <a href="#Destroying-Resources">Destroying Resources</a> •
  <a href="#Useful-Commands">Useful Commands</a> •
  <a href="#related">Related</a> •
  <a href="#Authors">Authors</a>
</p>

## Getting Started
To run an Ansible Playbook with AWX, you need to configure the following items
- AWS Credentials: User name/password or ssh key to connect to remote component

In order for Terraform to be able to make changes in your AWS account, you will need to set the AWS credentials for the IAM user you created earlier as the environment variables AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY:
> export AWS_ACCESS_KEY_ID=(your access key id)
> export AWS_SECRET_ACCESS_KEY=(your secret access key)
NOTE
Authentication options
In addition to environment variables, Terraform supports the same authentication mechanisms as all AWS CLI and SDK tools. Therefore, it’ll also be able to use credentials in ~/.aws/credentials, which are automatically generated if you are run the configure command on the AWS CLI, or IAM Roles, which you can add to almost any resource in AWS. For more info, see Configuring the AWS Command Line Interface.

- Project: It contains the Ansible playbook, config, roles, templates etc
- Inventories : What servers the playbook will run against and connection specific configuration
- Templates: Job template to associate all of the above and run the playbook
- Launch Templates: Launching current project

## Installing

You can download Terraform from the Terraform homepage
- https://www.terraform.io

Click the download link, select the appropriate package for your operating system, download the zip archive, and unzip it into the directory where you want Terraform to be installed. The archive will extract a single binary called terraform, which you’ll want to add to your PATH environment variable.
Run the terraform command, and you should see the usage instructions:

```bash
terraform --version
Terraform v0.11.13
```



## Deploying Resources

Terraform code is written in the HashiCorp Configuration Language (HCL) in files with the extension .tf. It is a declarative language, so your goal is to describe the infrastructure you want, and Terraform will figure out how to create it




As an example, the following curl command retrieves the list of AWX Job templates provisioned
```bash
let’s run a dirt-simple web server that always returns the text “Hello, World”:7
#!/bin/bash
echo "Hello, World" > index.html
nohup busybox httpd -f -p 8080 &
This is a bash script that writes the text “Hello, World” into index.html and runs a tool called busybox (which is installed by default on Ubuntu) to fire up a web server on port 8080 to serve that file. I wrapped the busybox command with nohup and & so that the web server runs permanently in the background, while the bash script itself can exit.
How do you get the EC2 Instance to run this script? Normally, as discussed in “Server templating tools”, you would use a tool like Packer to create a custom AMI that has the web server installed on it. Since the dummy web server in this example is just a one-liner, there is nothing to install. Therefore, in such a simple case, you can just run the script above as part of the EC2 Instance’s User Data configuration, which AWS will execute when the Instance is booting:
resource "aws_instance" "example" {
  ami = "ami-40d28157"
  instance_type = "t2.micro"

  user_data = <<-EOF
              #!/bin/bash
              echo "Hello, World" > index.html
              nohup busybox httpd -f -p 8080 &
              EOF

  tags {
    Name = "terraform-example"
  }
}

```

You need to do one more thing before this web server works. By default, AWS does not allow any incoming or outgoing traffic from an EC2 Instance. To allow the EC2 Instance to receive traffic on port 8080, you need to create a security group:

```terraform
resource "aws_security_group" "instance" {
  name = "terraform-example-instance"
  ingress {
    from_port = 8080
    to_port = 8080
    protocol = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```


```terraform
output "public_ip" {
  value = "${aws_instance.example.public_ip}"
}

```

```bash
> terraform apply

aws_security_group.instance: Refreshing state... (ID: sg-db91dba1)
aws_instance.example: Refreshing state... (ID: i-61744350)

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

public_ip = 54.174.13.5
```

## Destroying Resources

## Useful Commands
Docker offers the Docker Bench for Security script (https://github.com/docker/docker-bench-security) , which checks a Docker configuration against the published hardening guide: CIS DOCKER 1.12.0 BENCHMARK V1.0.0 
You can just download the script and run it straight from your host. Once you have run the script, you will be presented the output shown below

![](dockerSecurity.gif)


The script results in Info, Warning, and Pass notes for each of the recommendations which are grouped into 5 sections:
Host Configuration
Docker Daemon Configuration
Docker Daemon Configuration Files
Container Images and Build Files
Container Runtime

Once the reported is generated, you can follow the mentioned benchmark document to remediate them.


## Related
* [AWX](https://github.com/ansible/awx) - Configuration Management
 
## Authors
* **Javier Baltar** - *Initial work* - [GitHub](https://github.com/JavierBaltar)

