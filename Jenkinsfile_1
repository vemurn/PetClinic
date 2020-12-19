pipeline {
    
    agent {
        label 'master'
    }
    
    environment {
        MY_NAME = 'GOPI'
    }
    
    tools {
        jdk 'JAVA8'
        maven 'MAVEN3'
    }
    
    parameters {
        choice choices: ['DEV', 'SIT', 'UAT', 'PRD'], description: 'Environment Name', name: 'ENV'
        string defaultValue: '', description: 'The micro service name', name: 'APP_NAME', trim: false
    }
    
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '15')
        timestamps()
        disableConcurrentBuilds()
}
    
    stages {
        stage ('Stage 1'){
            steps {
                echo 'Welcome to Stage 1'
            }
        }
        
        stage ('Print env values'){
            steps {
                sh '''
                echo "The value provided to environment is $ENV"
                echo "The value provided for app name is $APP_NAME"
                '''
            }
        }
    }

}
