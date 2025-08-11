@Library('shared-lib')_
 
pipeline {
    agent any

    environment {
        DOCKER_USER = credentials('kariman-docker')
        DOCKER_PASS = credentials('password-docker')
        IMAGE_NAME = 'karimanmahmoud/app-java'
    }
    stages {
        stage('Build') {
            steps {
                script {
                    def mavenBuild = new org.iti.mvn()
                    mavenBuild.javaBuild("package install")
                }
            }
        }
        stage('Archive') {
            steps {
                script {
                    def archiveApp = new org.iti.archiveApp()
                    archiveApp.archive()
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    def dockerBuildApp = new org.iti.dockerBuild()
                    dockerBuildApp.dockerBuild(IMAGE_NAME, env.BUILD_NUMBER)
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    def dockerPushApp = new org.iti.dockerPush()
                    dockerPushApp.dockerPush(IMAGE_NAME, env.BUILD_NUMBER, env.DOCKER_USER, env.DOCKER_PASS)
                }
            }
        }
    }
}