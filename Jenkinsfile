pipeline {
  options {
    buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '0'))
  }
  agent {
    kubernetes {
      //cloud 'kubernetes'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven
    image: danielrmartin/sko:1.2
    command: ['cat']
    tty: true
"""
    }
  }
  parameters {
    choice(
      choices: ['deploy' , 'release'],
      description: '',
      name: 'FLOW_CONTEXT'
    )
  }
  stages {
    stage('Run Maven') {
      steps {
        container('maven') {
          sh 'mvn deploy -f ./complete/pom.xml'
        }
      }
    }
    stage ('Run Sonarqube'){
      steps{
        container('maven'){
          sh 'mvn sonar:sonar -f ./complete/pom.xml'
        }}}
  }
  post {
    success {
      script {
        if (params.FLOW_CONTEXT == 'deploy') {
          cloudBeesFlowRunPipeline addParam: '{"pipeline":{"pipelineName":"user32","parameters":[{"parameterName":"jenkinsBuildNumber","parameterValue":"${env.BUILD_NUMBER}"},{"parameterName":"jenkinsJobName","parameterValue":"${env.JOB_NAME}"},{"parameterName":"passingComplexity","parameterValue":""}]}}', configuration: 'flow-sko-jenkins-config', pipelineName: 'user32', projectName: 'user32_project'
        }
      }
    }
   }
}
