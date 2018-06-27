# Jenkins Pipelines Examples
Keep a few examples of Jenkins Pipelines. Mostly used for demo and training.

Look in each directory and find the `Jenkinsfile` with the example. See more details are in the `Jenkinsfile` comments.

**You are more than welcome to contribute, share and ask.**

## Setup Local Jenkins
These examples assume a simple setup of a single Jenkins master and two agents.

The setup is installed and run in a [Vagrant](https://www.vagrantup.com/) image.

### Spin up the Vagrant VM
Run the following in the root of the repository
```bash
# Spin up the Vagrant VM
$ vagrant up

# Once finished, ssh into VM
$ vagrant ssh
```

### Prepare variables

- Go into the directory that has the repository files
```bash
$ cd /opt/provisioning/
```

- Load ENVIRONMENT VARIABLES:
```bash
$ source env/vars.sh
```

### Start a local Jenkins master

- Prepare directories and permissions
```bash
# Permissions on /var/run/docker.sock
$ sudo chmod 666 /var/run/docker.sock

# Directory for Jenkins home
$ mkdir -p ~/jenkins_home
$ chmod -R 777 ~/jenkins_home
```

- Build the custom Jenkins Docker image
```bash
# Build Jenkins Docker Image
$ docker-compose -f env/docker-compose-jenkins.yml build
```

- Create custom Jenkins network (where master and agents will live in harmony)
```bash
$ docker network create jenkins_network
```

- Start the Jenkins master
```bash
$ docker-compose -f env/docker-compose-jenkins.yml up -d
```
- Browse to http://192.168.0.20:8080 and complete the initial setup wizard
  - Get initial admin password from `docker logs jenkins-master` output
  - Install suggested plugins
  - Install the **Docker Slaves Plugin** (for dynamic Docker agent provisioning per build)
  - Install the **Blue Ocean plugin** (recommended)
  - Define two Jenkins agents (slaves)
  - Create two nodes from the `Manage Jenkins` -> `Manage Nodes`
  - Call the nodes `agent1` and `agent2`
  - Set remote root directory to `/home/jenkins`
  - Configure them to `Launch method` -> `Launch agent via Java Web Start`
  - Get the secret token for each node (**AGENT1_TOKEN** and **AGENT2_TOKEN**)
  - Provide token in `env/vars.sh` file
  - Reload ENVIRONMENT VARIABLES:
  ```bash
  $ source env/vars.sh
  ```

### Start two Jenkins agents (slaves)
Once you have the tokens for the agents, start the two agents
```bash
$ docker-compose -f env/docker-compose-agents.yml up -d
```

### Create Pipeline jobs
You can create a Pipeline type job using any of the provided examples in the different directories

### Note:
Known bug for Docker Slaves Plugin
```bash
java.io.IOException: Failed to create docker image
```
How to solve:
`Manage Jenkins` -> `Configure System` -> `Docker Slaves` -> `Remoting image` should be `jenkins/slave`
