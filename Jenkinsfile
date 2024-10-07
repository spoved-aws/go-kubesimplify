pipeline {
    agent any
    
    environment {
        KO_DOCKER_REPO = 'docker.io/kanukhosla10/go-kubesimplify'
        KO_DEFAULTBASEIMAGE = 'alpine:3'
        GIT_COMMITTER_EMAIL = 'kanukhosla10@gmail.com'
        GIT_COMMITTER_NAME = 'spoved-aws'

        GO_PATH = '/usr/local/go/bin'
        KO_PATH = '/usr/local/bin'
        PATH = "${GO_PATH}:${KO_PATH}:${env.PATH}"
    }
    
    stages {
        
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/spoved-aws/go-kubesimplify.git'
            }
        }

        stage('Log in to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubCredentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo $DOCKER_PASSWORD | ko login docker.io --username $DOCKER_USERNAME --password-stdin'
                    sh 'pwd'
                    sh 'ls'
                }
            }
        }

        stage('Get git SHA short') {
            steps {
                script {
                    env.sha_short = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                }
            }
        }

        // stage('Build and Publish with ko') {
        //     steps {
        //         sh 'cd src && ls'
        //         sh 'pwd'
        //         sh "cd src/ && ko build --bare -t sha-${env.sha_short} ."
        //         sh 'pwd'
        //         sh 'cd ..'
        //         sh 'pwd'
        //     }
        // }

        stage('Generate deploy manifest from Jinja template') {
            steps {
                sh '''
                  python3 -m venv venv
                  . venv/bin/activate
                  pip install jinja2-cli
                  jinja2 k8s-manifests/tmpl/deploy.j2 -o k8s-manifests/deployments/deploy.yaml -D image_deploy_tag=sha-${sha_short} --strict
                  cat k8s-manifests/deployments/deploy.yaml
                  deactivate
                '''
            }
        }

        // stage('Configure git for the action') {
        //     steps {
        //         sh '''
        //           git config --local user.email "${GIT_COMMITTER_EMAIL}"
        //           git config --local user.name "${GIT_COMMITTER_NAME}"
        //         '''
        //     }
        // }

        // stage('Stash unstaged changes') {
        //     steps {
        //         sh 'git stash --include-untracked'
        //     }
        // }

        // stage('Pull latest changes from the remote branch') {
        //     steps {
        //         sh 'git pull origin main --rebase'
        //     }
        // }

        // stage('Apply stashed changes') {
        //     steps {
        //         sh 'git stash pop || echo "No stashed changes to apply"'
        //     }
        // }

        // stage('Commit deploy manifest on local repo') {
        //     steps {
        //         sh '''
        //           git add deploy/deploy.yaml
        //           git commit -s -m "[skip ci] Generate deployment manifests"
        //         '''
        //     }
        // }

        // stage('Push deploy manifests to remote repo') {
        //     steps {
        //         withCredentials([string(credentialsId: 'githubToken', variable: 'GITHUB_TOKEN')]) {
        //             sh '''
        //               git push origin main
        //             '''
        //         }
        //     }
        // }
    }
}