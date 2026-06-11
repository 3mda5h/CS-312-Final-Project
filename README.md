# Deploy a minecraft server on AWS with Ansible

## Background
Below is the official Acme Corp documentation for using Ansible scripts to deploy a company minecraft server on an EC2 instance. 
### What the script does:
- Creates an EC2 instance of type t3.small with the Amazon Linux 2023 operating system
- Creates a security group that allows traffic to port 22 (for SSH connections) and port 25565 (the default port used to connect to the Minecraft server)
- Installs Java 25 and downloads the Minecraft 26.1.2 server jar file to a directory titled "Minecraft-server"
- Runs the jar file and accepts the resulting eula
- Creates a systemd service that automatically runs the Minecraft server upon reboot

## Requirements
1. You must have a .pem private key stored on your local computer that you can use to connect with instances on AWS
2. You must have AWS credentials stored at `~/.aws/credentials`.
If you are using an AWS learned lab, you can get these credentials from the "AWS details" tab on the page where you start and stop the lab. Note that your credentials will change with each lab session. 

### Steps for deployment:
1. Clone the repository: `git clone git@github.com:3mda5h/CS-312-Final-Project.git`
2. Inside the project directory, create a python virtual environment: `python3 -m venv .venv`
if you do not have python installed, first run: `sudo apt install python3 python3.14-venv`
3. Activate the virtual environment: `source .venv/bin/activate`
4. Install dependencies: `sudo apt install ansible boto3 botocore` 
`ansible-galaxy collection install amazon.aws`
5. Configuration: 
    open `ec2.yml` and do the following:
    - change the value of `key_name` to the name of your key-pair on AWS 
    - change the value of `vpc_id` to the id of the VPC you want your instance to be deployed in (if you're not sure, any VPC or the default is fine)
    - change the value of `vpc_id` to the id of the subet you wish your instance to be deployed in (if you're not sure, any subnet or the default is fine)
6. To deploy the minecraft server, run `ansible-playbook deploy_mc_server.yml`. It may take a few minutes to finish. 
7. Once the ansible script is finished, it will take about 30 more seconds for the minecraft server start. You can ensure that the server is running by scanning the EC2 instance with nmap: `nmap -sV -Pn -p T:25565 <EC2 public ip>`. If the nmap results show that port 25565 is open and running Minecraft 26.1.2, then the server has successfully started! Alternatively, you can SSH into the instance and check if the server.jar process is running, or connect to the server IP in your Minecraft client.


## Resources
- https://medium.com/@a_tsai5/creating-an-ec2-instance-using-ansible-764cf70015f6
- https://stackoverflow.com/questions/20177996/ansible-playbook-to-run-shell-commands 
- https://ryan.himmelwright.net/post/foundryvtt-service-ansible-role/ 
- https://oneuptime.com/blog/post/2026-02-21-how-to-set-up-aws-credentials-for-ansible/view 