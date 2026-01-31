# Dumbmerch deployment


## 1 server provisioning & initial configuration


this section covers the initial environment preparation and server provisioning.


### server provisioning


<p alig"center"> <img src="1/1.png" widthn=="700" alt="command"> </p>


for this final task, the infrastructure is deployed using biznet giot neo lite with the following instances:

- app Server: dedicated for running the main application services.

- gateway server: dedicated as the entry point for traffic (reverse proxy).


### install wsl and env


to manage the infrastructure, i configured a local environment using WSL (windows subsystem for linux) and a dedicated python virtual environment for ansible:

- WSL Installation: bash wsl --install

- environment & ansible setup:

    sudo apt update && sudo apt install python3-venv -y

    python3 -m venv ansible-env

    source ansible-env/bin/activate

    pip install ansible


<p alig"center"> <img src="1/venv.png" widthn=="700" alt="command"> </p>


### attach SSH keys to server


<p alig"center"> <img src="1/ssh.PNG" widthn=="700" alt="command"> </p>


security is managed by implementing ssh key based authentication. i generated a pair of keys locally and attach the public key to the ~/.ssh/authorized_keys file on both the app and gateway servers to enable secure, passwordless access. the private key was then integrated into the WSL environment with proper permissions to ensure a seamless and secure connection for automation.


### create inventory file for playbook


<p alig"center"> <img src="1/1b.png" widthn=="700" alt="command"> </p>


to streamline the automation process, i created an inventory file to map the server roles and ip addresses. this inventory acts as the primary navigation for ansible to execute tasks. following this, i developed a structured ansible playbook designed to automate system updates, security hardening, and package installations across all provisioned instances.


<p alig"center"> <img src="1/1c.png" widthn=="700" alt="command"> </p>


## 2 version control system (VCS) setup


### create a repository on github


the development process began by cloning the provided Frontend (fe) and backend (be) repositories into the WSL environment. then create staging and production branches for both repositories to maintain code stability. make the repositories private on GitHub, configured SSH keys for secure authentication.


<p alig"center"> <img src="2/sshgit.PNG" widthn=="700" alt="command"> </p>


and pushed the local source code to their respective remote repositories.


<p align="center">
  <img src="2/1.png" width=="700" />
  <img src="2/1a.png" width=="700" />
</p>


### modify .env file


<p align="center">
  <img src="2/2.png" width=="700" />
  <img src="2/2a.png" width=="700" />
</p>


customized the .env files for both services to ensure seamless integration: the frontend was configured to communicate with the Backend api, and the backend was linked to the postgresql database.


### Add secrets to repository fe and be


<p align="center">
  <img src="2/4.png" width=="700" />
  <img src="2/4a.png" width=="700" />
</p>


to maintain security, sensitive information such as database credentials and server ip were not hardcoded; instead, they were stored securely as github secrets within each repository.


### create workflows file


<p align="center">
  <img src="2/3.png" width=="700" />
  <img src="2/3a.png" width=="700" />
</p>


to automate the deployment workflow, i implemented gitHub actions by creating custom workflow files in each repository. these pipelines are triggered upon pushes to the staging or production branches, ensuring that every code change is automatically tested, built, and ready for deployment to the servers.


## 3. server Hardening & user management


### create new user finaltask-gen1 and hardening


i automated the creation of a new administrative user, finaltask-gen1, across all servers using an ansible playbook. this user serves as the primary service account for all deployment tasks. 


<p alig"center"> <img src="3/hardening.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="3/hardeninga.png" widthn=="700" alt="command"> </p>


to ensure secure access, the playbook also manages only one SSH key for distribution, allowing the user to log in securely while maintaining proper permission levels.

to enhance server security, I implemented several network hardening measures via Ansible:

- ssh port customization: changed the default SSH port from 22 to 6969 to mitigate brute force attacks.

- firewall rules: configured ufw (uncomplicated firewall) to only allow essential traffic. specifically, i opened ports 3000, 3001, 5000, and 5001 for application access, while explicitly managing other incoming traffic to maintain a strict security posture.


<p alig"center"> <img src="1/1a.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="3/1.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="3/2.png" widthn=="700" alt="command"> </p>


### Create a working SSH config to log in into server


<p alig"center"> <img src="3/config.png" widthn=="700" alt="command"> </p>


to simplify the connection process, i created a local ssh config file. this configuration maps the hostnames for both the app and gateway servers, matching the user and custom ports defined in the ansible playbook. this setup allows for seamless access using a simple ssh app or ssh gateway command, ensuring consistency between the automation scripts and manual server management.


<p alig"center"> <img src="3/2a.png" widthn=="700" alt="command"> </p>


## 4. private docker registry deployment


### install docker


<p alig"center"> <img src="3/docker.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="3/adocker.png" widthn=="700" alt="command"> </p>


automated the installation of the docker engine across all servers using an ansible playbook. the installation process was specifically configured to integrate with the finaltask-gen1 user, including adding the user to the docker group. this allows for running container commands without requiring root privileges.


### deploy private registry on top docker


<p alig"center"> <img src="4/registry.png" widthn=="700" alt="command"> </p>


to manage custom application images, i deployed a private docker registry as a containerized service. using ansible playbook, the registry was configured to run on port 5000 with persistent storage. this private registry serves as the central hub for storing and distributing built images (Frontend and Backend) across the staging and production environments, ensuring faster and more secure deployment cycles.


<p alig"center"> <img src="5/dockercon.png" widthn=="700" alt="command"> </p>


## 5. database & application orchestration


### deploy database PostgreSQL


<p alig"center"> <img src="5/1.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="5/1a.png" widthn=="700" alt="command"> </p>


i deployed PostgreSQL on the app Server using Docker, exposed on the default port 5432. to ensure data persistence, i configured a docker volume mapped to the home directory of the service user (/home/finaltask-gen1/). the database credentials and environment variables were securely managed using GitHub Secrets, ensuring that sensitive data is injected dynamically during the deployment process.

<p alig"center"> <img src="5/dockercon.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="5/dblist.png" widthn=="700" alt="command"> </p>


### create docker file on each fe and be


<p alig"center"> <img src="5/dockerfile.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="5/dockerfile1.png" widthn=="700" alt="command"> </p>


for both frontend and backend services, i implemented multi stage docker builds. this approach significantly reduces the final image size by separating the build environment from the production runtime. The result is a lightweight, secure, and high performance container image ready for distribution via the private registry.


### create workflows on repositories fe and be


<p alig"center"> <img src="4/1.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="4/1a.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="4/2.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="4/2a.png" widthn=="700" alt="command"> </p>


i created dedicated deployment workflows (deployfe.yml and deploy.yml) within github actions to orchestrate the integration.

the workflow follows these critical steps:

- repository pull & Image build: automatically pulls the latest code and builds the image using the multi stage process.

- database migration: the backend pipeline includes a migration step to ensure the postgresql schema stays synchronized with the latest code changes.

- secure deployment: the pipeline connects to the server via ssh, pulls the newly built image from the private docker registry, and redeploys the application containers.

- integrity check: the process concludes by verifying the container status using docker ps to ensure all services are running correctly in the staging and production environments.

- frontend: configured to integrate seamlessly with the Backend api.

- backend: includes an automated database migration step to ensure the postgresql schema stays synchronized with the application code. the deployment was first pushed and verified on the staging branch, followed by a status check using docker ps to ensure all containers were running optimally.


<p alig"center"> <img src="5/dockercon.png" widthn=="700" alt="command"> </p>


check image on docker ps


<p alig"center"> <img src="5/fesuccess.png" widthn=="700" alt="command"> </p>


test register and login


<p alig"center"> <img src="5/db.png" widthn=="700" alt="command"> </p>


check db user


<p alig"center"> <img src="4/registeryfebe.png" widthn=="700" alt="command"> </p>


check image on private registry


## 6. CI/CD Pipeline Integration


### check pipeline branch staging on github actions


after pushing the code to the staging branch, i monitored the execution of the github actions pipeline to ensure deployment integrity. this step is crucial to verify that every stage starting from code linting, building the multi stage docker images, pushing to the private registry, to the final remote deployment is executed without errors.


<p alig"center"> <img src="2/3d.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/pipeline.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/pipeline1.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="2/3f.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="2/3e.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/bestaging.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/bestaging1.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/bestaging2.png" widthn=="700" alt="command"> </p>


the deployment is considered successful only when the pipeline returns a "green" status (passed) for all jobs. this visual confirmation in github actions serves as the first gate of validation, ensuring that the staging environment is running the latest stable version of the application before any further testing or promotion to production.


## 7. Security (SSL/TLS) & Domain Management


### install certbot


<p alig"center"> <img src="8/certbot.png" widthn=="700" alt="command"> </p>


to ensure secure communication across all services, i implemented certbot on the gateway server using ansible. i manually executed the certificate generation process to obtain privkey.pem and fullchain.pem. these certificates enable end to end encryption for all subdomains, providing a secure https environment for both development and production stages.


### install nginx


<p alig"center"> <img src="8/nginx.png" widthn=="700" alt="command"> </p>


i deployed and configured nginx as the primary gateway using an ansible playbook. 


### create domain using cloudflare


create domain using cloudflare: 

- gen.studentdumbways.my.id(fe production)
- api.gen.studentdumbways.my.id(be production)
- staging.gen.studentdumbways.my.id(fe staging)
- api.staging.gen.studentdumbways.my.id(be staging)
- exporter.gen.studentdumbways.my.id
- prom.gen.studentdumbways.my.id
- monitoring.gen.studentdumbways.my.id(grafana)


### set up reverse proxy 


<p alig"center"> <img src="8/2.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2a.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2b.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2c.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2d.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2e.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2f.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2g.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2h.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2i.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2j.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2k.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="8/2l.png" widthn=="700" alt="command"> </p>


the nginx configuration was optimized for high performance and security. every server block is configured to listen on port 443 with ssl enabled. i also implemented http/2 to improve loading speeds and ensured that all non https traffic is automatically redirected to a secure connection, maintaining a robust security posture across the entire infrastructure.


## 8. push to production branch


after verifying all services in the staging environment, i performed push of the updated workflows (deployfe and deploy(be)) and the entire source code to the production branch. this action triggers the high level ci/cd pipeline, initiating the automated build and deployment process for the live environment. 


<p alig"center"> <img src="6/beprod.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/beprod1.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/beprod2.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/production.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/feprod1.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="6/feprod2.png" widthn=="700" alt="command"> </p>


check website run on https and domain


<p alig"center"> <img src="5/prod.png" widthn=="700" alt="command"> </p>


by merging and pushing to the Production branch, i ensured that the latest stable features and security patches were seamlessly deployed to the production servers. this step completes the development lifecycle, moving the application from testing to a fully operational state, accessible via the production domains with full https encryption and integrated monitoring


## 9. Monitoring & Alerting System 


### install node exporter


<p alig"center"> <img src="7/1.png" widthn=="700" alt="command"> </p>


deployed node exporter on all servers to collect hardware and os metrics


### install prometheus


<p alig"center"> <img src="7/prome/prome.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="7/prome/scrape.png" widthn=="700" alt="command"> </p>


configured prometheus to scrape data from Node Exporter


<p alig"center"> <img src="7/prome/target.png" widthn=="700" alt="command"> </p>


verified the connection by checking the prometheus targets page


### install grafana


<p alig"center"> <img src="7/graf/grafana.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="7/graf/grafana1.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="7/graf/grafana1.png" widthn=="700" alt="command"> </p>


added prometheus as the primary data source to enable metric visualization.


### set up basic auth on prometheus


to secure the prometheus dashboard, i implemented basic auth through nginx:


<p alig"center"> <img src="7/prome/auth.png" widthn=="700" alt="command"> </p>


updated the system and installed apache2-utils on the gateway server using the following command sudo apt update && sudo apt install apache2-utils -y


<p alig"center"> <img src="7/prome/auth1.png" widthn=="700" alt="command"> </p>


created a credential file using the following command sudo htpasswd -c /etc/nginx/.htpasswd admin(as ausername) and enter the new password


<p alig"center"> <img src="7/prome/auth2.png" widthn=="700" alt="command"> </p>


integrated the authentication into the rproxy.yml (ansible):
auth_basic "Admin Area";
auth_basic_user_file /etc/nginx/.htpasswd;


<p alig"center"> <img src="7/prome/auth3.png" widthn=="700" alt="command"> </p>


auth is now successfully active and required to access the prometheus dashboard. 


### set up grafana dashboard and alert


<p alig"center"> <img src="8/graf/dashboard.png" widthn=="700" alt="command"> </p>


create dashboard:
- go to grafana dashboard menu and select Import 
- enter the id for "1860" for node exporter full 
- select the prometheus data source and click import
- customize the panels to display specific server resource metrics.


<p alig"center"> <img src="7/graf/1.png" widthn=="700" alt="command"> </p>


accessed botfather on telegram and executed the /newbot command


<p alig"center"> <img src="7/graf/1a.png" widthn=="700" alt="command"> </p>


started the bot with /start to initialize the session


<p alig"center"> <img src="7/graf/1c.png" widthn=="700" alt="command"> </p>


followed the setup to receive the http api Token and accessed the telegram api link via a browser to identify and copy the unique chat id


<p alig"center"> <img src="7/graf/2.png" widthn=="700" alt="command"> </p>


- create contact point configuration:
- navigated to the alerting menu in grafana and selected contact points
- chose telegram as the integration type
- entered the bot token and chat id then saved the configuration


<p alig"center"> <img src="7/graf/2a.png" widthn=="700" alt="command"> </p>


create alert rules


<p alig"center"> <img src="7/graf/2b.png" widthn=="700" alt="command"> </p>


defined specific alert rules by setting up queries and threshold conditions 


<p alig"center"> <img src="7/graf/kebakaran.png" widthn=="700" alt="command"> </p>


<p alig"center"> <img src="7/graf/kebakaran1.png" widthn=="700" alt="command"> </p>


executed a test notification to confirm that the alerting system successfully sends real time messages to the telegram bot
