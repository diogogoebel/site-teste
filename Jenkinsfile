stage 'Checkout'
 node() {
  deleteDir()
  checkout scm
 }

stage 'slack notification'
 node () {
  sh 'git log -1 --pretty=%B > commit-log.txt'                 
  GIT_COMMIT=readFile('commit-log.txt').trim() 
  slackSend channel: 'codehip', color: '#1e602f', message: "BUILD_INICIADO: PROJETO - ${env.JOB_NAME} - (${GIT_COMMIT})"
}

stage 'STG-Deploy'
 node () {
  openshiftBuild(buildConfig: 'frontend', showBuildLogs: 'true') 
 }

stage 'STG-Check'
 node () {
  openshiftVerifyBuild(buildConfig: 'frontend') 
 }

stage 'Tests'
 node () {
  sh 'curl http://frontend-jenkins.getup.io/ | grep -i html6 '
 }

stage 'Tag'
 node () {
   openshiftTag(srcStream: "java", srcTag: "latest", destStream: "java", destTag: "promoteqa")
 }

stage 'Prod Deploy'
 node () {
  openshiftBuild(buildConfig: 'prodfrontend', showBuildLogs: 'true')
 }

stage 'Prod Check'
 node () {
  openshiftVerifyBuild(buildConfig: 'prodfrontend') 
 }
