# Final Task


## 1 Server Provisioning & Initial Configuration


This section covers the initial environment preparation and server provisioning.


### server provisioning


<p alig"center"> <img src="1/1.png" widthn=="700" alt="command"> </p>


For this final task, the infrastructure is deployed using Biznet GIO NEO Lite with the following instances:

App Server: Dedicated for running the main application services.

Gateway Server: Dedicated as the entry point for traffic (Reverse Proxy).


### install wsl and env


To manage the infrastructure, I configured a local environment using WSL (Windows Subsystem for Linux) and a dedicated Python Virtual Environment for Ansible:

- WSL Installation: bash wsl --install

- Environment & Ansible Setup:

    sudo apt update && sudo apt install python3-venv -y

    python3 -m venv ansible-env

    source ansible-env/bin/activate

    pip install ansible


<p alig"center"> <img src="1/1c.png" widthn=="700" alt="command"> </p>


### attach SSH keys to server


<p alig"center"> <img src="1/ssh.PNG" widthn=="700" alt="command"> </p>


Security is managed by implementing SSH Key based authentication. I generated a pair of keys locally and attach the public key to the ~/.ssh/authorized_keys file on both the App and Gateway servers to enable secure, passwordless access. The private key was then integrated into the WSL environment with proper permissions to ensure a seamless and secure connection for automation.


### Create inventory file for playbook


<p alig"center"> <img src="1/1b.png" widthn=="700" alt="command"> </p>


To streamline the automation process, I created an inventory file to map the server roles and IP addresses. This inventory acts as the primary navigation for Ansible to execute tasks. Following this, I developed a structured Ansible Playbook designed to automate system updates, security hardening, and package installations across all provisioned instances.


<p alig"center"> <img src="1/1c.png" widthn=="700" alt="command"> </p>


## 2 Version Control System (VCS) Setup


### Create a repository on Github


The development process began by cloning the provided Frontend (FE) and Backend (BE) repositories into the WSL environment. Then create Staging and Production branches for both repositories to maintain code stability. make the repositories private on GitHub, configured SSH keys for secure authentication.


<p alig"center"> <img src="2/sshgit.PNG" widthn=="700" alt="command"> </p>


and pushed the local source code to their respective remote repositories.


<p align="center">
  <img src="2/1.png" width="60%" />
  <img src="2/1a.png" width="60%" />
</p>


### Modify .env file


<p align="center">
  <img src="2/2.png" width="48%" />
  <img src="2/2a.png" width="48%" />
</p>


customized the .env files for both services to ensure seamless integration: the Frontend was configured to communicate with the Backend API, and the Backend was linked to the PostgreSQL database.


### Add secrets to repository fe and be


<p align="center">
  <img src="2/4.png" width="48%" />
  <img src="2/4a.png" width="48%" />
</p>


To maintain security, sensitive information such as database credentials and server IPs were not hardcoded; instead, they were stored securely as GitHub Secrets within each repository.


### Create Workflows file


<p align="center">
  <img src="2/3.png" width="48%" />
  <img src="2/3a.png" width="48%" />
</p>


To automate the deployment workflow, I implemented GitHub Actions by creating custom workflow files in each repository. These pipelines are triggered upon pushes to the Staging or Production branches, ensuring that every code change is automatically tested, built, and ready for deployment to the servers.


## 3. Server Hardening & User Management
Create new user finaltask-gen
Server login with SSH key and Password
Create a working SSH config to log into servers
Only use 1 SSH keys for all purpose (Repository, CI/CD etc.)
UFW enabled with only used ports allowed
Change ssh port from (22) to (6969)


### langkah 1


<p align="center">
  <img src="" width="48%" />
  <img src="" width="48%" />
</p>




### langkah 2




<p align="center">
  <img src="" width="48%" />
  <img src="" width="48%" />
</p>




## 4. Private Docker Registry Deployment
Deploy Docker Registry Private on this server
Push your image into Your Own Docker Registry
reverse proxy for docker registry was registry.gen.studentdumbways.my.id


### langkah 1


<p alig"center"> <img src="" widthn=="700" alt="command"> </p>




### langkah 2


<p align="center">
  <img src="" width="48%" />
  <img src="" width="48%" />
</p>




<p alig"center"> <img src="" widthn=="700" alt="command"> </p>




## 5. Database & Application Orchestration


### langkah 1


<p alig"center"> <img src="" widthn=="700" alt="command"> </p>




### langkah 2


<p alig"center"> <img src="" widthn=="700" alt="command"> </p>




### langkah 3


<p alig"center"> <img src="" widthn=="700" alt="command"> </p>




### langkah 4


<p alig"center"> <img src="monitoring.png" widthn=="700" alt="command"> </p>




## 6. CI/CD Pipeline Integration


<p alig"center"> <img src="" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="" widthn=="700" alt="command"> </p>


di sini saya menggunakan cloudflare untuk https.
Final task


## 7. Monitoring & Alerting System


## 8. Security (SSL/TLS) & Domain Management
