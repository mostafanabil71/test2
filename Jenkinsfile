pipeline {
    agent any
    environment {
        registry = "mostafanabil71/test2"
        GOCACHE = "/tmp"
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'golang'
                    args '-v $GOPATH:/go'
                }
            }
            steps {
                sh '''
                    cd ${GOPATH}/src
                    mkdir -p ${GOPATH}/src/hello-world
                    cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world
                    cd ${GOPATH}/src/hello-world
                    go build
                '''
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'golang'
                    args '-v $GOPATH:/go'
                }
            }
            steps {
                sh '''
                    cd ${GOPATH}/src
                    mkdir -p ${GOPATH}/src/hello-world
                    cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world
                    cd ${GOPATH}/src/hello-world
                    go clean -cache
                    go test ./... -v -short
                '''
            }
        }
        stage('Publish') {
            environment {
                registryCredential = 'docker-hub-credentials'
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
        stage('Deploy') {
            steps {
                script {
                    def image_id = registry + ":$BUILD_NUMBER"
                    sh "ansible-playbook playbook.yml --extra-vars \"image_id=${image_id}\""
                }
            }
        }
    }
}
