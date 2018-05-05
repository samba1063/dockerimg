
node('new43') {
// Delete the workspace
//deleteDir()
     def app
stage('Retrieve source code') {
    checkout scm
    delivery = load 'repository.groovy'
    sh " cd $WORKSPACE;/bin/mkdir Build-${env.BUILD_NUMBER} "
    }
try {
    {
     stage('Maven Build') {
      docker.image('maven:3.5-jdk-8-alpine').inside {
        sh "mvn clean package -Dbuild.number=${BUILD_NUMBER}"
          sh "/bin/cp  $WORKSPACE/target/*.war $WORKSPACE/vsvyadav_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war"
        sh "/bin/mv -f $WORKSPACE/target/*.war $WORKSPACE/Build-${env.BUILD_NUMBER}/vsvyadav_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war"
       
      }
     }
          stage('build image') {
      {
        app = docker.build("shanmukha443/new43:docker${env.BUILD_NUMBER}")
        sh "/bin/mv -f $WORKSPACE/target/*.war $WORKSPACE/Build-${env.BUILD_NUMBER}/vsvyadav_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war"
       }
    }
          stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
        
    
   stage('Deploy') {
        sh "/bin/cp -f $WORKSPACE/Build-${env.BUILD_NUMBER}/vsvyadav_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war /opt/tomcat/apache-tomcat-9.0.7/webapps/vsvyadav.war"
    }
  
   delivery.artifactory()
        
       
  }
  catch (e) {
      currentBuild.result = "FAILED"
      throw e
    }
}

