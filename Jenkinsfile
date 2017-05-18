#!/usr/bin/groovy
@Library('github.com/fabric8io/fabric8-pipeline-library@v2.2.311')

def utils = new io.fabric8.Utils()

clientsNode{
  def envStage = utils.environmentNamespace('staging')
  def newVersion = ''

  git 'https://github.com/akubicharm/php-hello-world.git'
  stage 'Canary release'
  echo 'NOTE: running pipelines for the first time will take longer as build and base docker images are pulled onto the node'
  if (!fileExists ('Dockerfile')) {
    writeFile file: 'Dockerfile', text: 'FROM fabric8/php:onbuild'
  }

  newVersion = performCanaryRelease {}

  def rc = getKubernetesJson {
    port = 80
    label = 'php'
    icon = 'https://cdn.rawgit.com/fabric8io/fabric8/aebfa59/website/src/images/logos/php.png'
    version = newVersion
    imageName = clusterImageName
  }

  stage 'Rollout Staging'
  kubernetesApply(file: rc, environment: envStage)

}
