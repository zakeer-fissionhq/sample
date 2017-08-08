node {
    def server
    def buildInfo
    def rtGradle
    
    stage ('Build') {
        git url: 'https://github.com/jfrogdev/project-examples.git'
    }
 
    stage ('Artifactory configuration') {
        server = Artifactory.server SERVER_ID

        rtGradle = Artifactory.newGradleBuild()
        rtGradle.tool = GRADLE_TOOL // Tool name from Jenkins configuration
        rtGradle.deployer repo: 'libs-release-local', server: server
        rtGradle.resolver repo: 'libs-release', server: server
        rtGradle.deployer.deployArtifacts = false // Disable artifacts deployment during Gradle run
        
        buildInfo = Artifactory.newBuildInfo()
    }
 
    stage ('Test') {
        rtGradle.run rootDir: 'gradle-examples/4/gradle-example-ci-server/', buildFile: 'build.gradle', tasks: 'clean test'
    }
 
    stage ('Deploy') {
        rtGradle.run rootDir: 'gradle-examples/4/gradle-example-ci-server/', buildFile: 'build.gradle', tasks: 'artifactoryPublish', buildInfo: buildInfo
        rtGradle.deployer.deployArtifacts buildInfo
    }
    
    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}
