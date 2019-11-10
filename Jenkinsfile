#!groovy
pipeline{
    agent any
       //environment {
        //   BUILD_NUMBER="$BUILD_NUMBER"       }
       stages {
        stage('checkout') {
            steps {
                git url: 'https://github.com/ryzhan/ConfDemo3.git'
            }
        }   
        
        stage('build front-end') {
            steps {
                
                dir('./ansible'){
                    sh 'ansible-playbook build_microservices.yml --tags "front-end-build" --extra-var "BUILD_NUMBER=$BUILD_NUMBER"'
                }
                
            }
            
        }
           
        stage('Archive workspace') {
                steps {
                    archiveArtifacts artifacts: '**/*', fingerprint: true
                }
        }
           
        stage('run front-end') {
            steps {
                
                timeout(time:5, unit:'DAYS') {
                    input message:'Approve deployment?'
                }
                
                dir('./ansible'){
                    sh 'ansible-playbook build_microservices.yml --tags "front-end-run" --extra-var "BUILD_NUMBER=$BUILD_NUMBER"'
                }
                
            }
            
        }
        
       
 
    }
    
    post {
        always {
            echo 'I have finished'
            echo 'And cleaned workspace'
            //deleteDir()
        }
        success {
            echo 'Job succeeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}
