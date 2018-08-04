<b>Jenkins build and deploy agents</b><br/>
1. Create 2 Jenkins agents. One on the Swarm manager node and another one on a Swarm worker node.<br/>
2. The agent on the worker node will execute all stages in the Jenkinsfile except the deploy stage.<br/>
3. The agent on the master node will deploy the container by executing a stack deploy of the Docker compose file.<br/>
<br/>
<b>Nexus</b><br/>
1. Install Nexus or use an existing artifact repository.<br/>
<b>Sonar</b><br/>
1. Install Sonar for checking code quality or use an existing tool.<br/>
<br/>
<b>Docker Image tag and compose file</b><br/>
1. Jenkinsfile uses PROJECT_OWNER in pipeline.properties and artifactId, version in pom.xml to create the tag string for the docker image<br/>
2. The docker-compose.yml file retrieved from the application github repo is stashed in the worker node and unstashed during the deploy stage on the master node.<br/>
<br/>
<b>Quality Analysis</b><br/>
1. After compiling and unit testing the code, the code quality is checked against Sonar.<br/>
2. The file pipeline.properties has the Sonar endpoint details.<br/>
<br/>
<b>Build Jar file, Artifact repository and Docker Image</b><br/>
1. The app(petclinic at https://github.com/bewinsto7604/petclinic-demo.git) uses a multi-stage build Docker file.<br/>
2. During this build step the multi-stage Docker file will create the Jar file and push the Jar file to the Nexus artifact repository.The endpoint details for Nexus is available in the Maven pom file.<br/>
3. The second part of the Docker file will create the runtime image by including just the Jar file from the previous step within the image.<br/>
<br/>
<b>Image Signing and push to Docker hub</b><br/> 
1. Vault client will retrieve the snapshot key passphrase and set it in the DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE env variable.<br/>
2. The DOCKER_CONTENT_TRUST env vaiable is set to 1 enable image signing.<br/>
3. Docker push will sign image and push to Docker hub.<br/>
<br/>
<b>Deploy</b><br/>
1. Once the release is approved, the deploy stage will unstash the docker compose file on the master node and run the docker stack deploy command to bring up the containers<br/>
