pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                //...build steps here
            }
        }

        stage('Test') {
            steps {
                //...test steps here
            }
        }

        stage('Deliver') {
            agent any
            environment {
                VOLUME = '$(pwd)/sources:/src'
                IMAGE = 'cdrx/pyinstaller-linux:python2'
            }
            steps {
                dir(path: env.BUILD_ID) {
                    unstash(name: 'compiled-results')
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                }
            }
            post {
                success {
                    archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                }
            }
        }

        stage('Manual Approval') {
            steps {
                input(id: 'userInput', message: 'Lanjutkan ke tahap Deploy?')
            }
        }

        stage('Deploy') {
            steps {
                //...deploy steps here
                sh 'sleep 60'
            }
        }
    }

    post {
        success {
            echo 'Aplikasi berhasil dideploy dan siap digunakan!'
        }

        aborted {
            echo 'Pipeline dihentikan oleh user!'
        }
    }
}