pipeline {
    agent {
        label 'ROBOSHOP'
    }
    environment {
        appVersion = ""
        ACC_ID = "203733861426"
        region = "us-east-1"
    }
    options {
        // disableConcurrentBuilds()
        timeout(time: 5, unit: 'MINUTES')
    }
    // parameters {
    //     string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
    //     text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')
    //     booleanParam(name: 'DEPLOY', defaultValue: false, description: 'Toggle this value')
    //     choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
    //     password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    // }
    stages {
        stage('read version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                    echo "Version from package.json: ${appVersion}"
                }
            }    
        }
        stage('install dependencies') {
            steps {
                script {
                    sh """
                        npm install
                    """
                        echo "Installing dependencies..."
                }
            }
        }
        stage('build image') {
            steps {
                withAWS(credentials: 'aws-creds', region: ${region} ) {
                    sh """
                        aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.${region}.amazonaws.com
                        docker build -t roboshop/catalogue .
                        docker tag roboshop/catalogue:latest ${ACC_ID}.dkr.ecr.${region}.amazonaws.com/roboshop/catalogue:latest
                        docker push ${ACC_ID}.dkr.ecr.${region}.amazonaws.com/roboshop/catalogue:latest
                    """
                }
            }

        }
        // stage('test') {
        //     steps {
        //         script {
        //              echo "testing the project..."
        //              echo "Hello ${params.PERSON}"
        //              echo "Biography: ${params.BIOGRAPHY}"
        //              echo "Toggle: ${params.DEPLOY}"
        //              echo "Choice: ${params.DEPLOY}" 
        //              echo "Password: ${params.PASSWORD}" 
        //          }
        //     }
        // }
        // stage('deploy') {
        //     when {
        //         expression { "${params.DEPLOY}" == "true" }
        //     }
        //     input {
        //         message "Should we continue?"
        //         ok "Yes, we should."
        //         submitter "alice,bob"
        //         parameters {
        //             string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
        //         }
        //     } 
        //     steps {
        //         script {
        //              echo "deploying the project..."
        //          }
        //     }
        // }
    }
    // post build
    post { 
        always { 
            echo 'I will always say Hello again!'
        }
        success {
            echo "pipeline success"
        }
        failure {
            echo "pipeline failure"
        }
    }
}