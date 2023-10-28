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
      }
      archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
      dockerCmd = "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
      sh "${dockerCmd}" 
      sshagent(credentials: ['b157a2a1-6bc6-432a-bd3c-5a85a0fb959a']){
        echo "hello world!"
      }
      // sshagent(['25066cfa-1c15-48ef-a8f1-563112ac9703']) {
      //   sh "ssh -o StrictHostKeyChecking=no ec2-user@18.143.66.200 ${dockerCmd}"
      //   // sh "ssh -o StrictHostKeyChecking=no ec2-user@18.143.66.200 echo 'Hello World!'"
      // }
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