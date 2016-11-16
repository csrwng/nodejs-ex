node {
  stage 'Initialize'
  sh 'oc get route nodejs-mongodb-example -n $PROJECT_NAME -o jsonpath="{ .spec.to.weight }" > blueweight'
  blueWeight = readFile('blueweight').trim()
  tag = "blue"
  if (blueWeight == "100") {
    tag = "green"
  }
  sh "oc get route ${tag}-nodejs-mongodb-example -n $PROJECT_NAME -o jsonpath='{ .spec.host }' > routehost"
  routeHost = readFile('routehost').trim()
  echo "building tag ${tag}"

  stage 'Build'
  openshiftBuild(buildConfig: 'nodejs-mongodb-example', showBuildLogs: 'true')

  stage 'Deploy Test'
  openshiftTag(srcStream: 'nodejs-mongodb-example', srcTag: 'latest', destinationStream: 'nodejs-mongodb-example', destinationTag: tag)
  openshiftVerifyDeployment(deploymentConfig: 'nodejs-mongodb-example-'+tag)
}

stage 'Test'
input "Test deployment available. Do you approve?"

stage 'Go Live'
node {
  if (tag == "blue") {
    sh 'oc set -n $PROJECT_NAME route-backends nodejs-mongodb-example nodejs-mongodb-example-blue=100 nodejs-mongodb-example-green=0'
  } else {
    sh 'oc set -n $PROJECT_NAME route-backends nodejs-mongodb-example nodejs-mongodb-example-blue=0 nodejs-mongodb-example-green=100'
  }
}
