  
pipeline {
   agent any

   tools {
      // Install the Maven version configured as "maven" and add it to the path.
      maven "maven"
   }

   stages {
      stage('Ready') {
         steps {
            input 'Let\'s go?'
         }
      }
      stage('Build') {
         steps {
            // Get some code from a GitHub repository
            git 'https://github.com/SoleneBDev/SpoilGotApp.git'

            // Run Maven on a Unix agent.
            sh "mvn -Dmaven.test.failure.ignore=true clean package"

            // To run Maven on a Windows agent, use
            // bat "mvn -Dmaven.test.failure.ignore=true clean package"
         }

         post {
            // If Maven was able to run the tests, even if some of the test
            // failed, record the test results and archive the jar file.
            success {
               junit '**/target/surefire-reports/TEST-*.xml'
               archiveArtifacts 'target/*.war'
            }
         }
      }
      stage('S3') {
         steps {
            s3Upload consoleLogLevel: 'INFO',
                     dontSetBuildResultOnFailure: false,
                     dontWaitForConcurrentBuildCompletion: false,
                     entries: [[
                        bucket: 'boc-ic-bucket',
                        excludedFile: '',
                        flatten: false,
                        gzipFiles: false,
                        keepForever: false,
                        managedArtifacts: false,
                        noUploadOnFailure: false,
                        selectedRegion: 'eu-west-2',
                        showDirectlyInBrowser: false,
                        sourceFile: 'date.txt',
                        storageClass: 'STANDARD',
                        uploadFromSlave: false,
                        useServerSideEncryption: false]],
                     pluginFailureResultConstraint: 'FAILURE',
                     profileName: 'boc-profile',
                     userMetadata: []
         }
      }
   }
}