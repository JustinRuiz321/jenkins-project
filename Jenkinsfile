pipeline {
    agent any
    tools {
        nodejs "node"
    }
    stages {
        stage('increment version') {
            steps {
                script {
                    dir("app") {
                        sh "npm version minor"

                        def packageJson = readJSON file: 'package.json'
                        def version = packageJson.version

                        env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                    }
                    # env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                }
            }
        }
        
        stage('run tests') {
            steps {
               script {
                    dir("app") {
                        sh "npm install"
                        sh "npm run test"
                    } 
               }
            }
        }
        stage('Build and Push docker image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]){
                    sh "docker build -t docker-hub-id/myapp:${IMAGE_NAME} ."
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push jruiz321/docker-project:${IMAGE_NAME}"
                }
            }
        }
        stage('commit version update') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'gitlab-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh 'git config --global user.email "jenkins@me.com"'
                        sh 'git config --global user.name "jenkins"'
                        sh "git remote set-url origin https://$USER:$PASS@github.com/JustinRuiz321/jenkins-project.git"
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:jenkins-jobs'
                    }
                }
            }
        }
    }
}

