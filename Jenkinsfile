pipeline {
  agent any
  stages {
    stage("检出代码") {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: env.GIT_BUILD_REF]],
          userRemoteConfigs: [[
            url: env.GIT_REPO_URL,
            credentialsId: env.CREDENTIALS_ID
        ]]])
      }
    }

    stage('安装依赖') {
      steps {
        sh '''
            python -V
            pip install simiki
            wget http://gosspublic.alicdn.com/ossutil/1.7.3/ossutil64 -O /usr/local/bin/ossutil
            chmod 0755 /usr/local/bin/ossutil
        '''
        withCredentials([cloudApi(credentialsId: 'ca25bc86-c0dc-4b7f-a9a9-3074cfce12fa', secretIdVariable: 'Ali_Key', secretKeyVariable: 'Ali_Secret')]) {
            sh '''
                tee $HOME/.ossutilconfig > /dev/null <<EOF
                [Credentials]
                language=CH
                endpoint=oss-cn-hangzhou.aliyuncs.com
                accessKeyID=${Ali_Key}
                accessKeySecret=${Ali_Secret}
                EOF
            '''
        }
      }
    }
    stage('构建') {
      steps {
        sh 'simiki g'
      }
    }
    stage('同步') {
      steps {
        script {
            ls -l output
            ossutil ls
        }
      }
    }
  }
}