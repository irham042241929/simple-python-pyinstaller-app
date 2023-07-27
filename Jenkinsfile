pipeline {
        agent any
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
                        post {
                                success {
                                        echo "Build Berhasil"
                                }
                                failure {
                                        echo "Build Gagal"
                                }
                                aborted {
                                        echo "Build Batal"
                                }
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
                                success {
                                        echo "Test Berhasil"
                                }
                                failure {
                                        echo "Test Gagal"
                                }
                                aborted {
                                        echo "Test Batal"
                                }
                                always {
                                        junit 'test-reports/results.xml'
                                }
                        }
                }
                stage('Manual Approval') {
                        steps {
                                timeout(time: 30, unit: 'SECONDS'){
                                        input message: 'Lanjutkan ke tahap Deploy? (Klik "Proceed" untuk mengakhiri)'
                                }
                        }
                        post {
                                success {
                                        echo "Approval disetujui"
                                }
                                failure {
                                        echo "Approval gagal"
                                }
                                aborted {
                                        echo "Approval dibatalkan"
                                }
                        }
                }
                stage('Deploy') {
                        agent any
                        environment {
                                VOLUME = '$(pwd)/sources:/src'
                                IMAGE = 'cdrx/pyinstaller-linux:python2'
                        }
                        steps {
                                echo "Jeda 1 menit..."
                                sleep 60
                                echo "Jeda selesai, lanjut deploy..."
                                dir(path: env.BUILD_ID) {
                                        unstash(name: 'compiled-results')
                                        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                                }
                        }
                        post {
                                success {
                                        archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                                        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                                        echo "Deploy Berhasil"
                                }
                                failure {
                                        echo "Deploy Gagal"
                                }
                                aborted {
                                        echo "Deploy Batal"
                                }
                        }
                }
        }
}
