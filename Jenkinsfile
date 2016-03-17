#!/usr/bin/groovy
node{

  def dependencyPipeline
  def testProjectPipeline
  def stagedDependencyProject
  def stagedTestProject
  def pipelineTestProjectPrId

  stage 'Stage pipeline-test-project-dependency'
  git 'https://github.com/fabric8io/pipeline-test-project-dependency.git'
  dependencyPipeline = load 'release.groovy'
  stagedDependencyProject = dependencyPipeline.stage()

  stage 'Stage pipeline-test-project'
  git 'https://github.com/fabric8io/pipeline-test-project.git'
  sh "git remote set-url origin git@github.com:fabric8io/pipeline-test-project.git"
  testProjectPipeline = load 'release.groovy'
  pipelineTestProjectPrId = testProjectPipeline.updateDependencies('https://oss.sonatype.org/content/repositories/staging/')
  stagedTestProject = testProjectPipeline.stage()

  stage 'Deploy pipeline-test-project'
  testProjectPipeline.deploy(stagedTestProject)

  stage 'Approve release'
  testProjectPipeline.approveRelease(stagedTestProject)

  stage 'Promote release'
  dependencyPipeline.release(stagedDependencyProject)
  testProjectPipeline.release(stagedTestProject)
  testProjectPipeline.mergePullRequest(pipelineTestProjectPrId)

}
