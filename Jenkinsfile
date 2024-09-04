@Library('jenkins-library@main')_

pipeline {
    agent any
    tools {
        nodejs "node"
    }
    stages {
        stage('increment version') {
            steps {
                script {
                    incrementVersion()
                    }
                }
            }
        }
        
        stage('run tests') {
            steps {
               script {
                runTests()
               }
            }
        }
        stage('Build and Push docker image') {
            steps {
                dockerBuild()
            }
        }
        stage('commit version update') {
            steps {
                script {
                    gitCommit()
                }
            }
        }
}


