pipeline {
      environment {

        registry = "karimslaimi/timesheet"

        registryCredential = 'karimslaimi'

        dockerImage = 'timesheet'


    }
  agent any

  stages {
    stage('Checkout GIT') {
      steps {
        echo "pulling";
        git url: "https://github.com/karimsla/Time-sheets";
      }
    }
    stage("Test, Build and sonar") {
      steps {
        bat "mvn package sonar:sonar"
      }

    }
    stage("deploy to nexus"){
        steps{
            bat "mvn clean package deploy:deploy-file -DgroupId=tn.esprit.spring -DartifactId=Timesheet-spring-boot-core-data-jpa-mvc-REST-1 -Dversion=2.3.12.RELEASE -DgeneratePom=true -Dpackaging=jar -DrepositoryId=deploymentRepo -Durl=http://localhost:8081/repository/maven-releases/ -Dfile=target/Timesheet-spring-boot-core-data-jpa-mvc-REST-1-2.3.12.RELEASE.jar"
        }
    }
    stage("build and deploy to docker"){
        steps{

             script {


                    dockerImage = docker.build registry + ":$BUILD_NUMBER"

                    docker.withRegistry( '', registryCredential ) {

                        dockerImage.push()

                    }



                     bat "docker-compose build timesheet"
                     bat "docker tag karimslaimi/timesheet:latest karimslaimi/timesheet:$BUILD_NUMBER"
                     bat "docker-compose push timesheet"

                     bat " docker-compose up --build -d --force-recreate"

                }

        }
    }
  }
    post {
      always {


            emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                mimeType: 'text/html',
                subject: "[Jenkins] ${env.JOB_NAME}",
                to: "mohamedkarim.slaimi@esprit.tn",

                recipientProviders: [[$class: 'CulpritsRecipientProvider']]

        echo 'I will always say Hello again!'


      }
    }


}