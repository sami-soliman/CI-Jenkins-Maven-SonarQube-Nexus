pipeline{
    
    agent any

    tools {
        // Install the Maven version configured as "maven" and add it to the path.
        maven "maven"
    }

    stages {    
        stage('Git Checkout'){
            steps{
                script{
                    
                    git branch: 'main', url: 'https://github.com/sami-soliman/CI-Jenkins-Maven-SonarQube-Nexus.git'
                }
            }
        }
        stage('Unit Test'){
            steps{
                sh '''#!/bin/bash
                 echo "performing uint testing"
                 mvn test
               '''
            }
        }
        stage('Integration testing'){           
            steps{              
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){         
            steps{              
                script{  
                    sh 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){         
            steps{
                script{  
                    withSonarQubeEnv(credentialsId: 'sonarqube') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }
                }
            }
        stage('Quality Gate Status'){
                steps{
                    script{  
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube'
                    }
                }
            }
        stage('UPload realse artifact to nexus'){
                steps{
                    
                    script{
                        def version = sh script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true
                        def database = version.endsWith("SNAPSHOT") ? "db-snapshot" : "db-release"
                        nexusArtifactUploader artifacts: [[artifactId: 'springboot', classifier: '', file: 'target/Uber.jar', type: 'jar']],
                        credentialsId: 'nexus',
                        groupId: 'com.example',
                        nexusUrl: '172.19.0.5:8081',
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        repository: database,
                        version: "${version}"
                    }
                }
            }
        stage('build docker image'){
            steps{
                script{
                    sh 'docker image build -t $JOB_NAME:v-$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v-$BUILD_ID kubesami/$JOB_NAME:v-$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:v-$BUILD_ID kubesami/$JOB_NAME:latest'
                }
            }
        }
        stage('push docker image to HUB'){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'password', usernameVariable: 'username')]) {
                        sh 'docker login -u $username -p $password'
                        sh 'docker image push kubesami/$JOB_NAME:v-$BUILD_ID'
                        sh 'docker image push kubesami/$JOB_NAME:latest'
                    }
                }
            }
        }
            
        }
}