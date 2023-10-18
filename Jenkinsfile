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

node {
  stage('Build'){
    docker.image('python:3.12.0-alpine3.18').inside{
      sh 'python -m py_compile sources/add2vals.py sources/calc.py'
      stash(name: 'compiled-results', includes: 'sources/*.py*')
    }
  }
  stage('Test'){
    docker.image('qnib/pytest').inside{
      try{
        sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
      } catch(e) {
        echo 'Test stage failed'
      } finally {
        junit 'test-reports/results.xml'
      }

    }
  }
  stage('Deliver'){
    withEnv([VOLUME = '$(pwd)/sources:/src', IMAGE = 'cdrx/pyinstaller-linux:python2']){    
      // try {
        dir(path: env.BUILD_ID) { 
          unstash(name: 'compiled-results') 
          sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
        // }
        archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'" 
      // } catch(e){
      //   echo 'Deliver stage failed';
      // }
    }
  }
}

// pipeline {
//     agent none
//     options {
//         skipStagesAfterUnstable()
//     }
//     stages {
//         stage('Build') {
//             agent {
//                 docker {
//                     image 'python:3.12.0-alpine3.18'
//                 }
//             }
//             steps {
//                 sh 'python -m py_compile sources/add2vals.py sources/calc.py'
//                 stash(name: 'compiled-results', includes: 'sources/*.py*')
//             }
//         }
//         stage('Test') {
//             agent {
//                 docker {
//                     image 'qnib/pytest'
//                 }
//             }
//             steps {
//                 sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
//             }
//             post {
//                 always {
//                     junit 'test-reports/results.xml'
//                 }
//             }
//         }
//         stage('Deliver') { 
//             agent any
//             environment { 
//                 VOLUME = '$(pwd)/sources:/src'
//                 IMAGE = 'cdrx/pyinstaller-linux:python2'
//             }
//             steps {
//                 dir(path: env.BUILD_ID) { 
//                     unstash(name: 'compiled-results') 
//                     sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'" 
//                 }
//             }
//             post {
//                 success {
//                     archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
//                     sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
//                 }
//             }
//         }
//     }
// }


// pipeline {
//     agent none
//     stages {
//         stage('Build') {
//             agent {
//                 docker {
//                     image 'python:2-alpine'
//                 }
//             }
//             steps {
//                 sh 'python -m py_compile sources/add2vals.py sources/calc.py'
//             }
//         }
//         stage('Test') {
//             agent {
//                 docker {
//                     image 'qnib/pytest'
//                 }
//             }
//             steps {
//                 sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
//             }
//             post {
//                 always {
//                     junit 'test-reports/results.xml'
//                 }
//             }
//         }
//         stage('Deliver') {
//             agent any
//             environment {
//                 VOLUME = '$(pwd)/sources:/src'
//                 IMAGE = 'cdrx/pyinstaller-linux:python2'
//             }
//             steps {
//                 dir(path: env.BUILD_ID) {
//                     unstash(name: 'compiled-results')
//                     sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
//                 }
//             }
//             post {
//                 success {
//                     archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
//                     sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
//                 }
//             }
//         }

        // stage('Deliver') {
        //     agent {
        //         docker {
        //             image 'cdrx/pyinstaller-linux:python3'
        //         }
        //     }
        //     steps {
        //       echo 'Hello World!'
        //         // sh 'pyinstaller --onefile sources/add2vals.py'
        //     }
        //     post {
        //         success {
        //             archiveArtifacts 'dist/add2vals'
        //         }
        //     }
        // }
//     }
// }