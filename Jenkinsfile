pipeline {
    agent any

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {
        stage('Info') {
            steps {
                echo "Building branch: ${env.BRANCH_NAME} (build #${env.BUILD_NUMBER})"
            }
        }

        stage('Compile') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'rm -rf build && mkdir -p build && javac -d build *.java'
                    } else {
                        bat 'if exist build rmdir /s /q build & mkdir build & javac -d build *.java'
                    }
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'jar cf tp-git.jar -C build .'
                    } else {
                        bat 'jar cf tp-git.jar -C build .'
                    }
                }
            }
        }

        // Runs only on master: shows that each branch can behave differently.
        stage('Deploy') {
            when { branch 'master' }
            steps {
                echo 'Deploying from master (simulated)...'
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'tp-git.jar', fingerprint: true
            echo "Branch ${env.BRANCH_NAME}: SUCCESS"
        }
        failure {
            echo "Branch ${env.BRANCH_NAME}: FAILED - check the Compile stage log"
        }
    }
}
