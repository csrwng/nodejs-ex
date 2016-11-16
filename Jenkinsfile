node {
  stage 'init'
  sh 'oc get route nodejs-mongodb-example -o jsonpath="{ .spec.to.weight }" > blueweight'
  blueWeight = readFile('blueweight').trim()
  tag = "blue"
  if (blueWeight == "100") {
    tag = "green"
  }
  echo "building tag ${tag}"

  stage 'build'
  openshiftBuild(buildConfig: 'nodejs-mongodb-example', showBuildLogs: 'true')

  stage 'deploy-test'
  openshiftTag(srcStream: 'nodejs-mongodb-example', srcTag: 'latest', destinationStream: 'nodejs-mongodb-example', destinationTag: tag)
  verifyDeployment(deploymentConfig: 'nodejs-mongodb-example-'+tag)
}

stage 'test'
input 'Do you approve the deployment?'

stage 'deploy'
node {
  if (tag == "blue") {
    sh 'oc set route-backends blue=100 green=0'
  } else {
    sh 'oc set route-backends blue=0 green=100'
  }
}
