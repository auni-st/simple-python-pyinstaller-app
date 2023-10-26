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
  stage('Manual Approval'){
    input message: 'Lanjutkan ke tahap Deploy?'
  }
  stage('Deploy'){
    VOLUME = '$(pwd)/sources:/src'
    IMAGE = 'cdrx/pyinstaller-linux:python2'

    try {
      dir(path: env.BUILD_ID) { 
        unstash(name: 'compiled-results') 
        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
        echo "Hello World!"
        echo "Hello World !!!!!!"
      }
      archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
      sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'" 
      sleep(60)
    } catch(e){
      echo 'Deploy stage failed';
    }
  }

  // stage('Deliver'){
  //   VOLUME = '$(pwd)/sources:/src'
  //   IMAGE = 'cdrx/pyinstaller-linux:python2'

  //   try {
  //     dir(path: env.BUILD_ID) { 
  //       unstash(name: 'compiled-results') 
  //       sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
  //     }
  //       archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
  //       sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'" 
  //   } catch(e){
  //     echo 'Deliver stage failed';
  //   }
  // }
}