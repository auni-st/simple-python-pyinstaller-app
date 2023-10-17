// node {
//     stage('Build'){
//         docker.image('python:2-alpine').inside {
//             sh 'python -m py_compile sources/add2vals.py sources/calc.py'
//         }
//     }  
//     stage('Test'){
//         try {
//             docker.image('qnib/pytest').inside {
//                 sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
//             }
//         } catch (e) {
//             echo 'Test failed.'
//         } finally {
//             junit 'test-reports/results.xml'
//         }
//     }
//     stage('Deliver'){
//         try {
//             docker.image('cdrx/pyinstaller-linux:python2').inside {
//                 sh 'pyinstaller --onefile sources/add2vals.py'
//             }
//             archiveArtifacts 'dist/add2vals'
//         } catch (e) {
//             echo 'Deliver failed';
//         }        
//     }
// }


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
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python3'
                }
            }
            steps {
              // echo 'Hello World!'
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}