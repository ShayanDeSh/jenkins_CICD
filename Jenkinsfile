pipeline {
    agent any
    environment {
        registry = "nothingrealm/jenkins_cicd"
        GOCACHE = "/tmp"
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'golang'
                }
            }
            steps {
                sh 'cd ${GOPATH}/src'
                sh 'mkdir -p ${GOPATH}/src/hello-world'
                sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
                sh 'go build'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'golang'
                }
            }
            steps {
                sh 'cd ${GOPATH}/src'
                sh 'mkdir -p ${GOPATH}/src/hello-world'
                sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
                sh 'go clean -cache'
                sh 'go test ./... -v -short'
            }
        }
        stage('Publish') {
            environment {
                registrycredential = 'nothingrealm'
            }
            steps {
                script {
                    def appimage = docker.build registry + ":$BUILD_NUMBER"
                    docker.withRegistry('', registryCredential) {
                        appimage.push()
                        appimage.push('latest')
                    }
                }
            }
        }
        stage ('Deploy') {
            steps {
                script {
                    def image_id = registry + ":$BUILD_NUMBER"
                    sh "python --version"
                    sh "pip install openshift"
                    sh "ansible-playbook ./ansible/playbook.yml --extra-vars \"image_id=${image_id}\""
                }
            }
        }
    }
}
