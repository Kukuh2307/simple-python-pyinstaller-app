pipeline {
    agent none 
    stages {
        stage('Build') { 
            agent {
                docker {
                    image 'python:2-alpine' 
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
                stash(name: 'compiled-results', includes: 'sources/*.py*') 
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        // MANUAL APROVAL11
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
            steps {
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