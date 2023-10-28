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
    SSH_PRIVATE_KEY = credentials('b157a2a1-6bc6-432a-bd3c-5a85a0fb959a')

    try {
      dir(path: env.BUILD_ID) { 
        unstash(name: 'compiled-results') 
        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
      }
      archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
      dockerCmd = "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
      sh "${dockerCmd}" 
      sshagent(credentials: ['b157a2a1-6bc6-432a-bd3c-5a85a0fb959a']){
        sh "scp -v -o StrictHostKeyChecking=no ${env.BUILD_ID}/sources/dist/add2vals ec2-user@18.143.66.200:"
      }
      sleep(60)
    } catch(e){
      currentBuild.result = 'FAILURE'
      throw e
    }
  }
}