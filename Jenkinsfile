pipeline {
    agent any
    environment {
        DOCKER_ADDR = 'unix:///var/run/docker.sock'
        IMAGE_NAME = 'ubuntu_test'
        REGISTRY_ADDR = 'my.registry.com'
    }

    stages{
        stage('Clone repository') {
              steps {
            checkout scm
         }
        }
    }

        stage('Build image') {
            steps {
                 
                sh 'echo "FROM ubuntu:18.04\nLABEL env=dev" > Dockerfile'
                script {
                    docker.withServer("${env.DOCKER_ADDR}") {
                        image = docker.build("${env.IMAGE_NAME}:${env.BUILD_NUMBER}")
                    }
                }
            }
        }

        stage('Scan image') {
            steps {
                 
                prismaCloudScanImage ca: '',
                    cert: '',
                    dockerAddress: "${env.DOCKER_ADDR}",
                    ignoreImageBuildTime: true,
                    image: "${env.IMAGE_NAME}:${env.BUILD_NUMBER}",
                    key: '',
                    logLevel: 'debug',
                    podmanPath: '',
                    project: '',
                    resultsFile: 'prisma_cloud_scan_results.json'
            }
        }

        stage('Test image') {
            steps {
                
                script {
                    docker.withServer("${env.DOCKER_ADDR}") {
                        image.inside {
                            sh 'echo "Tests passed"'
                        }
                    }
                }
            }
        }

        // stage('Push image') {
        //         // Push image to registry with two tags: the build number and 'latest'
        //         docker.withRegistry("${env.REGISTRY_ADDR}") {
        //             image.push("${env.BUILD_NUMBER}")
        //             image.push('latest')
        //         }
        //     }
        // }
    }

    post {
        always {
            // Always publish scan results, regardless of 
            prismaCloudPublish resultsFilePattern: 'prisma_cloud_scan_results.json'
        }
    }

