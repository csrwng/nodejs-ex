node {
  stage 'init'
  sh 'oc get route nodejs-mongodb-example -n $PROJECT_NAME -o jsonpath="{ .spec.to.weight }" > blueweight'
  blueWeight = readFile('blueweight').trim()
  tag = "blue"
  if (blueWeight == "100") {
    tag = "green"
  }
  sh 'oc get route ${tag}-nodejs-mongodb-example -o jsonpath "{ spec.host }" > routehost'
  routeHost = readFile('routehost').trim()
  echo "building tag ${tag}"

  stage 'build'
  openshiftBuild(buildConfig: 'nodejs-mongodb-example', showBuildLogs: 'true')

  stage 'deploy-test'
  openshiftTag(srcStream: 'nodejs-mongodb-example', srcTag: 'latest', destinationStream: 'nodejs-mongodb-example', destinationTag: tag)
  openshiftVerifyDeployment(deploymentConfig: 'nodejs-mongodb-example-'+tag)
}

stage 'test'
input 'Test deployment available at http://${routehost}. Do you approve?'

stage 'deploy'
node {
  if (tag == "blue") {
    sh 'oc set -n $PROJECT_NAME route-backends blue=100 green=0'
  } else {
    sh 'oc set -n $PROJECT_NAME route-backends blue=0 green=100'
  }
}
