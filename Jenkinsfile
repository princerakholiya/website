pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:1234567890'
       appRegistry = "891215906691.dkr.ecr.ap-south-1.amazonaws.com/capstoneproject"
       capstoneRegistry = "https://891215906691.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "CapstoneProject"
        service = "capstone_service"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/princerakholiya/website'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '1234567890', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
