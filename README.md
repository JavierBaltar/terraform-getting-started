# terraform-getting-started
<p align="center">
  <a href="#Getting-Started">Getting Started</a> •
  <a href="#Installing">Installing</a> •
  <a href="#API">API</a> •
  <a href="#Notifications">Notifications</a> •
  <a href="#Docker-Security">Docker Security</a> •
  <a href="#related">Related</a> •
  <a href="#Authors">Authors</a>
</p>

## Getting Started
To run an Ansible Playbook with AWX, you need to configure the following items
- Credentials: User name/password or ssh key to connect to remote component
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


#### List users
`tower-cli user list`
#### Create a new user
`tower-cli user create --username=javierbaltar --first-name=Javier --last-name=Baltar --email=javierbaltar@mydomain.com`

#### Launch a job
`tower-cli job launch — job-template=id`

#### Monitor a job
`tower-cli job monitor id`

#### Export all objects 
`tower-cli receive — all`

#### Export all objects and save to a file in json format
`tower-cli receive — all — format json > awx.json`

#### Import from a JSON file named awx.json 
`tower-cli send assets.json`

#### Copy all assets from one instance to another
`tower-cli receive — tower-host awx.lab.com — all | tower-cli send — tower-host awx.production.com`

## API
This section offers a basic understanding of the REST API used by AWX and Ansible Tower
REST APIs provide access to resources (data entities) via URI paths. 
- https://docs.ansible.com/ansible-tower/2.3.0/html/towerapi/intro.html

You can visit the AWX REST API in a web browser at http://<AWX Server IP>/api/ as shown below:
  
![](awx-api.png)

As an example, the following curl command retrieves the list of AWX Job templates provisioned
```bash
export CREDENTIAL='admin:password'
curl -s  -k  -u $CREDENTIAL "http://AWX-IP/api/v2/job_templates/" | jq '.results | .[] | .name '
"IOS Change mgcp call agent"
"Retrieve IOS Running Config to File"
```
Similarly, list the AWX inventories 
```bash
curl -s -k -u $CREDENTIAL http://AWX-IP/api/v2/inventories/ | jq '.results | .[] | .name'

"Customer Webservers"
"Customer Databases"
```
Create a new AWX user
```bash
curl -H "Content-type: application/json" -d "$(jo username=jbaltar first_name=Javier last_name=Baltar email=jbaltar@mydomain.com password=dontshareit)" -u $CREDENTIAL http://AWX-IP/api/v2/users/
```

## Notifications
AWX notifications provide a mechanism of signaling when AWX jobs succeed or fail. This can take the form of sending a message to a Slack channel, an email or sending an HTTP POST to another service to trigger other actions.
In AWX the following notification types are supported:
- Email
- Slack
- Hipchat
- Pagerduty
- Twilio
- IRC
- Webhook (POST)

![](awx-notifications.png)

## Docker Security
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

