# Downloading and launching Suricata docker image  
## To download the docker image stored in the ECR: 

* Authenticate with AWS CLI and do a docker login by running the commands and following the instructions underneath:
``` 
aws configure
```
fill in credential as instructed to.
```
aws ecr get-login --no-include-email
```
Copy the output of the command and run it as root.

* Pull the image with docker pull e.g. 
```
sudo docker pull <aws id>.dkr.ecr.eu-west-1.amazon.com/alpine/suricata:latest 
```

## To run the Suricata docker image. One of the following methods are tested and recommended: 
* The following is a list of optional and required arguments: 
```
-it (interactive mode) (optional) 
-e CONFIG_NAME=<suricata configuration name>  (required) (NOTE! do not include the extension ".yaml") 
-e BUCKET_URI=<Source bucket>  (required) (where the configuration file resides) 
-e ARGS="<suricata launch arguments>  (optional) (default is: "-i eth0") 
-e AWS_KEY=<aws_access_key_id>  (required) 
-e AWS_REGION=<bucket region>  (optional) (default is "eu-west-1") 
```
#### Depending on method: 

##### Method 1: 
```
--secret <secret name> (required) (must run in swarm) 
```
##### OR 

##### Method 2: 
```
-e AWS_SECRET_KEY=<aws_secret_access_key> (required) 
```

### (Preferred) Method 1. Docker service with secret key, in Docker secret: 
* Initiate a docker swarm to use docker secret (creates single node swarm)
``` 
Sudo docker swarm init 
```

* Create a docker secret:
``` 
printf "<content/key>" | sudo docker secret create <secret name> - (NOTE! include the dash at the end) 
```

* Create the docker service which runs the image: 
```
sudo docker service create --name <name of service> --secret <secret name> -e CONFIG_NAME="suricata-config-1.0-dev" -e BUCKET_URI="s3://suricata-config-bucket" -e ARGS="-i eth0" -e AWS_KEY="xxxxx" -e AWS_REGION="eu-west-1"  1234567891234.dkr.ecr.eu-west-1.amazon.com/alpine/suricata:latest 
```

### Method 2. Docker run with only environment variables: 
* Run the image with the following command and arguments 
```
sudo docker run <arguments> <image name e.g. <aws id>.dkr.ecr.eu-west-1.amazon.com/alpine/suricata:latest>
```
* example of full command: 
```
sudo docker run -it -e CONFIG_NAME="suricata-config-1.0-dev" -e BUCKET_URI="s3://suricata-config-bucket" -e ARGS="-i eth0" -e AWS_KEY="xxxxx" -e AWS_SECRET_KEY="xxxxxx" -e AWS_REGION="eu-west-1" 1234567891234.dkr.ecr.eu-west-1.amazon.com/alpine/suricata:latest 
```

* (optional) 
Check the logs, to confirm the arguments and success: 
```
sudo docker ps
``` 
Grab the CONTAINER ID and run the command:
``` 
sudo docker logs <CONTAINER ID>
```