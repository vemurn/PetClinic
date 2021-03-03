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
        
        stage('Print Jenkins Vars'){
            steps {
                echo "Value of App Version is $app_version"
                echo "Value of App Env is $app_env"
                sh "env"
            }
        }
        
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
        stage('Upload Artifact'){
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-repo-creds', usernameVariable: 'user', passwordVariable: 'passwd')]){
                    sh '''
                    curl -u $user:$passwd POST "http://ec2-34-229-71-30.compute-1.amazonaws.com:8081/service/rest/v1/components?repository=PetClinic" -H "accept: application/json" -H "Content-Type: multipart/form-data" -F "maven2.groupId=org.springframework.samples" -F "maven2.artifactId=petclinic" -F "maven2.version=${BUILD_ID}.0.0" -F "maven2.asset1=@${WORKSPACE}/target/petclinic.war" -F "maven2.asset1.extension=war"
                    '''
                }
            }
        }
        stage('Deploy Code'){
            steps {
                ansiblePlaybook installation: 'ANSIBLE29', playbook: '/opt/ansible/deploy.yaml'
            }
        }
    }
}
