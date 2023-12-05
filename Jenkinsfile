pipeline {
    agent {
        node {
            label 'maven'
        }
    }

    stages {
        stage('Clean') {
            steps {
                // Remove all contents from the workspace directory without using sudo
                deleteDir()
            }
        }
        stage('Clone code') {
            steps {
                git branch: 'main', url: 'https://github.com/andynze1/tweet-trend-new.git'
            }
        }
        stage('Clean /tmp Directory') {
            steps {
                // Remove files from the /tmp directory
                sh 'rm -rf /tmp/*'
            }
        }
        stage('Check space') {
            steps {
                sh 'df -h'
            }
        }
    }
}

