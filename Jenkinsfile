pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building the application...'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing the application...'
            }
        }

        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input message: 'Lanjutkan ke tahap Deploy?', submitter: '*'

                    if (userInput == "Abort") {
                        error('Pengguna menghentikan pipeline.')
                    }
                }
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

        stage('Deploy') {
            agent any
            steps {
                script {
                        echo 'Deploying the application to production...'

                        sleep time: 60, unit: 'SECONDS'

                        echo 'Aplikasi berhasil di-deploy.'
                }
            }
        }
    }
}