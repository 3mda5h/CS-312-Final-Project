
# Deploy a minecraft server on AWS with Ansible

## Background

Below is the official Acme Corp documentation for using Ansible scripts to deploy a company Minecraft server on an EC2 instance.
### What the script does:

- Creates an EC2 instance of type t3.small with the Amazon Linux 2023 operating system
- Creates a security group that allows traffic to port 22 (for SSH connections) and port 25565 (the default port used to connect to the Minecraft server)
- Installs Java 25 and downloads the Minecraft 26.1.2 server jar file to a directory titled "Minecraft-server"
- Runs the jar file and accepts the resulting eula
- Creates a systemd service that automatically runs the Minecraft server upon reboot
## Requirements

1. You must have a `.pem` private key stored on your local computer that you can use to connect with instances on AWS
2. You must have AWS credentials stored at `~/.aws/credentials`. If you are using an AWS learned lab, you can get these credentials from the "AWS details" tab on the page where you start and stop the lab. Note that your credentials will change with each lab session.
### Steps for deployment:

1. Clone the repository: `git clone git@github.com:3mda5h/CS-312-Final-Project.git`
2. Inside the project directory, create a python virtual environment: `python3 -m venv .venv`.
	if you do not have python installed, first run: `sudo apt install python3 python3.14-venv`
3. Activate the virtual environment: `source .venv/bin/activate`
4. Install dependencies: 
    `sudo apt install ansible boto3 botocore`
    `ansible-galaxy collection install amazon.aws`

5. Configuration:
    open `ec2.yml` and do the following:
    - change the value of `key_name` to the name of your key-pair on AWS
    - change the value of `vpc_id` to the id of the VPC you want your instance to be deployed in (if you're not sure, any VPC or the default is fine)
    - change the value of `vpc_id` to the id of the subet you wish your instance to be deployed in (if you're not sure, any subnet or the default is fine)
5. To deploy the Minecraft server, run `ansible-playbook deploy_mc_server.yml`. It may take a few minutes to finish.
6. Once the ansible script is finished, it will take about 30 more seconds for the Minecraft server to start. You can ensure that the server is running by scanning the EC2 instance with nmap: `nmap -sV -Pn -p T:25565 <EC2 public ip>`. If the nmap results show that port 25565 is open and running Minecraft 26.1.2, then the server has successfully started!

### Connecting to the Minecraft server

To connect to the Minecraft server, open your Minecraft client and select "Multiplayer." Then, at the bottom of the screen, click "direct connection". Enter the public IP of the EC2 instance (no need to enter port number), and click "connect!"

### Pipeline

- User runs script `deploy_mc_server.yml`
- Script sequentially triggers the 3 scripts: `ec2.yml`, `mc_server.yml`, and `auto-start.yml`
- Ansible yaml runs on your local machine
- `amazon.aws` Python module imports boto3
- boto3 makes API calls to AWS using the credentials you provide in `~/.aws/credentials` to create the EC2 instance
- Once the instance is created, ansible SSHs into the EC2 instance (using the .pem you provide) 
- In the SSH session, Ansible runs shell commands that download the Minecraft server, as well as create a systemd service that starts the server
## Resources

- https://medium.com/@a_tsai5/creating-an-ec2-instance-using-ansible-764cf70015f6
- https://stackoverflow.com/questions/20177996/ansible-playbook-to-run-shell-commands
- https://ryan.himmelwright.net/post/foundryvtt-service-ansible-role/
- https://oneuptime.com/blog/post/2026-02-21-how-to-set-up-aws-credentials-for-ansible/view
- https://ibrahims.medium.com/boto3-python-a86fe5e8b01d