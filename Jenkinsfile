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
        
        stage('build and run front-end') {
            steps {
                
                dir('./ansible'){
                    sh 'ansible-playbook build.yml --tags "front-end-build" --extra-var "BUILD_NUMBER=$BUILD_NUMBER"'
                }
                
            }
            
        }
        
       
 
    }
}
