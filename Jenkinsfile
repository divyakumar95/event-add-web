// prerequisites: a nodejs app must be deployed inside a kubernetes cluster
// TODO: look for all instances of [] and replace all instances of 
//       the 'variables' with actual values 
// variables:
//      [GITREPO] https://github.com/Pa-van/events-app-api-server.git
//      [PROJECTID] roidtcmar101
//      [CLUSTER_NAME] dk-kuber-cluster 
//      [ZONE] us-east1-b
//      the following values can be found in the yaml:
//      [DEPLOYMENT_NAME] api-server-image
//      [CONTAINER_NAME] demo-api (in the template/spec section of the deployment)

pipeline {
    agent any 
    stages {
        stage('Stage 1') {
            steps {
                echo 'Retrieving source from github' 
                git branch: 'main',
                    url: 'https://github.com/Pa-van/events-app-api-server.git'
                echo 'Did we get the source?' 
                sh 'ls -a'
            }
        }
        stage('Stage 2') {
            steps {
                echo 'workspace and versions' 
                sh 'echo $WORKSPACE'
                sh 'gcloud version'
                sh 'node -v'
                sh 'npm -v'
        
            }
        } 
         stage('Stage 3') {
            environment {
                PORT = 8081
            }
            steps {
                echo 'install dependencies' 
                sh 'npm install'
                echo 'Run tests'
                sh 'npm test'
        
            }
        }        
         stage('Stage 4') {
            steps {
                echo "build id = ${env.BUILD_ID}"
                echo 'Tests passed on to build Docker container'
                sh "gcloud builds submit -t gcr.io/roidtcmar101/api-server:v2.${env.BUILD_ID} ."
            }
        }        
         stage('Stage 5') {
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials dk-kuber-cluster --zone us-east1-b --project roidtcmar101'
                echo 'Update the image'
                echo "gcr.io/roidtcmar101/internal:2.${env.BUILD_ID}"
                sh "kubectl set image deployment/demo-api demo-api=gcr.io/roidtcmar101/api-server:v2.${env.BUILD_ID} --record"
            }
        }
    }
}