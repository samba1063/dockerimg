
node('Node1') {
// Delete the workspace
//deleteDir()
stage('Retrieve source code') {
    checkout scm
    delivery = load 'repository.groovy'
    sh " cd $WORKSPACE;/bin/mkdir Build-${env.BUILD_NUMBER} "
    }
try {
    stage('Maven Build') {
      docker.image('maven:3.5-jdk-8-alpine').inside {
        sh "mvn clean package -Dbuild.number=${BUILD_NUMBER}"
        sh "/bin/mv -f $WORKSPACE/target/*.war $WORKSPACE/Build-${env.BUILD_NUMBER}/samba_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war"
        sh "/bin/cp -f $WORKSPACE/Build-${env.BUILD_NUMBER}/samba_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war $WORKSPACE/samba.war"
       }
    }
}
    
    
    environment {
    registry = “shanmukha443/docker”
    registryCredential = ‘docker-samba’
    dockerImage = ‘’
  }
          stage('build image') {
        dockerImage = docker.build registry + “:$BUILD_NUMBER"
       }
   
          stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('', registryCredential) {
            dockerImage.push()
           
         }
          }
    
   stage('Deploy') {
        sh "/bin/cp -f $WORKSPACE/Build-${env.BUILD_NUMBER}/samba_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war /opt/apache-tomcat-8.5.33/webapps/samba.war"
    }
  
   delivery.artifactory()
        
      
  catch (e) {
      currentBuild.result = "FAILED"
      throw e
    }
}

