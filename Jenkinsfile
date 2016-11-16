node('nodejs') {
stage 'checkout'
scm checkout
sh 'git rev-parse --verify HEAD > commitid'
commitId = readFile(commitid).trim()

stage 'build'
openshiftBuild(buildConfig: 'nodejs-mongodb-example', showBuildLogs: 'true', commitID: commitId)

stage 'deploy'
openshiftDeploy(deploymentConfig: 'nodejs-mongodb-example')
}
