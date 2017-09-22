#!/usr/bin/groovy
@Library('github.com/fabric8io/fabric8-pipeline-library@v2.2.311')
def utils = new io.fabric8.Utils()
println utils
clientsNode{
  def envStage = utils.environmentNamespace('staging')
  println envStage
  def newVersion = ''

  git 'http://gogs.default.9.110.178.111.nip.io/gogsadmin/testcreatingnodejs'

  stage 'Canary release'
  echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
  if (!fileExists ('Dockerfile')) {
    writeFile file: 'Dockerfile', text: 'FROM node:5.3-onbuild'
  }

  newVersion = performCanaryRelease {}

  def rc = getKubernetesJson {
    port = 8080
    label = 'nodejs'
    icon = 'https://cdn.rawgit.com/fabric8io/fabric8/dc05040/website/src/images/logos/nodejs.svg'
    version = newVersion
    imageName = clusterImageName
  }

  stage 'Rollout Staging'
  kubernetesApply(file: rc, environment: envStage)

}
