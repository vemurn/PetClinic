pipeline {
    
    agent {
        label 'master'
    }
    
    tools {
          maven 'MAVEN3'
          jdk 'JAVA8'
    }
    
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')
        disableConcurrentBuilds()
        timestamps()
    }
    
    parameters {
        string defaultValue: '', description: 'Version of the java application', name: 'app_version', trim: false
        choice choices: ['DEV', 'QA', 'INT', 'PRE_PROD'], description: 'Environment name for the code deployment', name: 'app_env'
    }
    
    stages {
        stage('Code Checkout'){
            steps {
                echo "code checkout"
                git url: 'https://github.com/gopishank/PetClinic.git'
            }
        }
        
        stage('Code Build'){
            steps {
                sh "mvn test-compile"
            }
        }
        stage('Code Quality'){
            environment {
                SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv ( installationName: 'sonarqube') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
                }
            }
        }
        stage('Maven Package'){
            steps {
                sh "mvn package"
            }
        }
        stage('Build Image'){
            steps {
                sh '''
                docker image build -t ec2-34-229-71-30.compute-1.amazonaws.com:9090/petclinic:$BUILD_ID .
                docker image push ec2-34-229-71-30.compute-1.amazonaws.com:9090/petclinic:$BUILD_ID
                '''
            }
        }
    }
}
