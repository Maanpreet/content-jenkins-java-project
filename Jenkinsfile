pipeline {
  agent none


  stages {
    stage('Unit test'){
      agent {
        label 'slave'
      }
      steps{
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build') {
      agent {
        label 'slave'
      }
      steps {
          sh 'ant -f build.xml -v'
        }
        post {
          #it means only succeed if above is success
          success {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
          }
        }
    }


    stage('Deploy'){
      agent {
        label 'slave'
      }
      steps{
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
    }

    stage('Running on CentOS'){
      agent {
        label 'centos'
      }
      steps{
        sh "wget http://10.32.1.77/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
  }



}
