# Downloading and launching Suricata docker image  <br />
## To download the docker image stored in the ECR: <br />
<br />
<br />
Authenticate with AWS CLI and do a docker login <br />
<aws configure> (fill in credential as instructed to) <br />
Copy the output of the command <aws ecr get-login --no-include-email> and run it as a command as root. <br />
It will start with e.g <sudo docker login> <br />
<br />
Pull the image with docker pull e.g. <br />
<sudo docker pull <aws id>.dkr.ecr.eu-west-1.amazon.com/alpine/suricata:latest> <br />
<br />
<br />
<br />
## To run the docker image stored in the ECR one of the following methods are tested and recommended: <br />
The following is a list of optional and required arguments: <br />
"-it" (interactive mode) (optional) <br />
"-e CONFIG_NAME="<suricata configuration name>" " (required) (NOTE! do not include the extension ".yaml") <br />
"-e BUCKET_URI="<Source bucket" " (required) (where the configuration file resides) <br />
"-e ARGS="<suricata launch arguments>" " (optional) (default is: "-i eth0") <br />
"-e AWS_KEY="<aws_access_key_id>" " (required) <br />
"-e AWS_REGION="<bucket region>" " (optional) (default is "eu-west-1") <br />
<br />
depending on method: <br />
<br />
### Method 1: <br />
"-e AWS_SECRET_KEY="<aws_secret_access_key>" "(required) <br />
<br />
OR <br />
<br />
### Method 2: <br />
"--secret <secret name>" (required) (must run in swarm) <br />
<br />
Method 1. docker run with only environment variables: <br />
Run the image with the following command and arguments <br />
"sudo docker run <arguments> <image name e.g. <aws id>.dkr.ecr.eu-west-1.amazon.com/alpine/suricata:latest> " <br />
<br />
<br />
example of full command: <br />
"sudo docker run -it -e CONFIG_NAME="suricata-config-1.0-dev" -e BUCKET_URI="s3://suricata-config-bucket" -e ARGS="-i eth0" -e AWS_KEY="xxxxx" -e AWS_SECRET_KEY="xxxxxx" -e AWS_REGION="eu-west-1" 1234567891234.dkr.ecr.eu-west-1.amazon.com/alpine/suricata:latest " <br />
<br />
<br />
(Preferred) Method 2. docker service with secret key in docker secret: <br />
Initiate a docker swarm to use docker secret (creates single node swarm): <br />
"Sudo docker swarm init " <br />
<br />
Create a docker secret: <br />
"printf "<content/key>" | sudo docker secret create <secret name> - "(NOTE! include the dash) <br />
<br />
Create the docker service which runs the image: <br />
"sudo docker service create --name <name of service> --secret <secret name> -e CONFIG_NAME="suricata-config-1.0-dev" -e BUCKET_URI="s3://suricata-config-bucket" -e ARGS="-i eth0" -e AWS_KEY="xxxxx" -e AWS_REGION="eu-west-1"  1234567891234.dkr.ecr.eu-west-1.amazon.com/alpine/suricata:latest " <br />
<br />
(optional) <br />
Check the logs, to confirm the arguments and success: <br />
<br />
"sudo docker ps" <br />
Grab the CONTAINER ID and run the command: <br />
"sudo docker logs <CONTAINER ID>" <br />
