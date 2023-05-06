pipeline {
    agent any
    environment {
        def BUILDVERSION = sh(script: "echo `date +%Y%m%d`", returnStdout: true).trim()
        def BUILDFULLNAME = "${BUILDVERSION}_1.0.${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Full Name Stage') {
               steps {

                    sh 'echo "Current build version :: ${BUILDFULLNAME}"'
                    sh 'echo "BUILDFULLNAME=${BUILDFULLNAME}" > .env'
                    sh 'echo "${BUILDFULLNAME}" >> Versioning.txt'

               }
        }

        stage('Docker Compose Pull and Build') {
               steps {
                    
                    sh 'docker-compose build --pull' 

               }
        }

        stage('Publish images to Docker Hub') {

                steps {
          
                    withDockerRegistry([ credentialsId: "dockerHub", url: "" ]) {
                    
                        sh  'docker-compose push'
                    
                    }
                }
        }

        stage('Run Docker containers on Jenkins Agents') {

                steps {
                    
                    sh 'docker run -d -p 4030:80 970922/centos'
                    sh 'docker run -d -p 4040:80 970922/ubuntu'
                    sh 'docker run -d -p 4050:80 970922/nginx'

                
                }
        }
        
        stage('Push changes to Versioning file to match DockerHub and Github'){
                steps {

                    withCredentials([gitUsernamePassword(credentialsId: 'gitpls', url: "")]) {

                        sh 'git add Versioning.txt'
                        sh 'git commit -m "Updated versioning file ${BUILDFULLNAME}"'
                        sh 'git push origin HEAD:mateusz'

                    }
                }
        }
    }
}
