//START-OF-SCRIPT
node {
    def server = Artifactory.server 'artifactory'
    
    withCredentials([usernamePassword(credentialsId: 'artifactory',
                     usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
        server.username = "${USERNAME}"
        server.password = "${PASSWORD}"
    }    
    
    def rtGradle = Artifactory.newGradleBuild()
    def buildInfo

    stage ('Clone') {
        git url: 'https://github.com/nrejepov/devops-webapp.git'
    }

    stage ('Artifactory Configuration') {
        rtGradle.tool = "gradle" // Tool name from Jenkins configuration
        rtGradle.deployer repo: 'gradle-release-local', server: server
        rtGradle.resolver repo: 'jcenter', server: server
    }

    stage ('Gradle Build') {
        sh '/var/jenkins_home/tools/hudson.plugins.gradle.GradleInstallation/gradle/bin/gradle clean build --no-daemon --info'

    }

    stage ('Gradle Publish') {
        buildInfo = rtGradle.run rootDir: "./", tasks: 'artifactoryPublish'
    }

    stage ('Artifactory Publish Build Info') {
        server.publishBuildInfo buildInfo
    }
}
//END-OF-SCRIPT