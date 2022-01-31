pipeline {
    agent any
    
	//agent {
        //docker {
         //   image 'maven:3.8.1-adoptopenjdk-11'
         //   args '-v /root/.m2:/root/.m2'
       // }
   // }
	
	
   tools { 
	// Global tools to be used by the pipeline
     maven 'maven3.6' 
       jdk 'jdk9' 
   }
	
    stages {
	stage('Unit Tests') {
	    // Run Unit tests 
            steps{
                sh 'mvn -f pom.xml clean test'
            }
        }	
        stage ('Artifactory configuration') {
            steps {
		// specify Artifactory server
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: "http://172.18.0.4:8081",
		    credentialsId: 'Admin.Artifactory'
                )
		// specify the repositories to be used for deploying the artifacts in the Artifactory
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )
		// defines the dependencies resolution details
                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
            }
        }
        stage ('Build & Upload Artifact') {
	    // run Maven Build and upload the built artifact to Artifactory
            steps {
                rtMavenRun (
                    tool: "maven3.6", // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }
        stage ('Publish build info') {
	    // Publish the build info in the Artifactory
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
                )
            }
        }
    }
}
