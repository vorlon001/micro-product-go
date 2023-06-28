pipeline {

    agent any

    tools {
        go '1.20.5'
    }
    environment {
        GO114MODULE = 'on'
        CGO_ENABLED = 0 
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    }

    stages {
        stage("unit-test") {
            steps {
                echo 'UNIT TEST EXECUTION STARTED'
                sh 'make unit-tests'
            }
        }
        stage("functional-test") {
            steps {
                echo 'FUNCTIONAL TEST EXECUTION STARTED'
                sh 'make functional-tests'
            }
        }
        stage("build") {
            environment {
                HARBOR = credentials('HARBORAUTH')
                REGESTRY  = credentials('harboriblog')
            }
            steps {
                echo 'BUILD EXECUTION STARTED'
                sh 'go version'
                sh 'go get ./...'
                sh 'docker build . -t $REGESTRY/test/product-go-micro'
            }
        }
        stage('Docker Push') {
            agent any
            environment {
                HARBOR = credentials('HARBORAUTH')
                REGESTRY  = credentials('harboriblog')
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'HARBORAUTH', passwordVariable: 'dockerhubPassword', usernameVariable: 'dockerhubUser')]) {
                sh '''
                df -h
                hostname
                '''
                sh 'docker login $REGESTRY --username=$HARBOR_USR --password=$HARBOR_PSW'
                sh 'docker push $REGESTRY/test/product-go-micro'
                }
            }
        }
    }
}
