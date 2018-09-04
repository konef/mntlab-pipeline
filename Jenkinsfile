node ('gradle') {
    
      stage('Creating Jobs') {

        build job: 'DSL script job'
      }
    
      stage('Preparation') {

        echo "==> Preparation stage begins."

        git branch: 'ikonev', url: 'https://github.com/konef/mntlab-pipeline.git'

    }

    
    stage('Building code') {

        sh ("/opt/gradle/gradle-4.10/bin/gradle build")

        echo ('Finished: Building code')

    }

    stage('Testing code') {

        parallel cucumber: {

            sh ("/opt/gradle/gradle-4.10/bin/gradle cucumber")

        },

        jacoco: {

            sh ("/opt/gradle/gradle-4.10/bin/gradle jacocoTestReport")

        },

        gradle: {

            sh ("/opt/gradle/gradle-4.10/bin/gradle test")

        }

        echo ('Finished: Testing code')

    }

    stage('Triggering job and fetching artefact after finishing') {

        build job: 'MNTLAB-{ikonev}-child1-build-job',

        parameters: [string(name: 'BRANCH_NAME', value: 'ikonev')]

        step ([$class: 'CopyArtifact',

                  projectName: 'MNTLAB-{ikonev}-child1-build-job',

                  filter: 'ikonev_dsl_script.tar.gz']);

            echo ('Finished: Triggering job and fetching artefact after finishing')

    }

    stage('Packaging and Publishing results') {

            sh ("tar -xzf ikonev_dsl_script.tar.gz && cp build/libs/*.jar ./")

            archiveArtifacts artifacts: 'gradle-simple.jar, Jenkinsfile, jobs.groovy', onlyIfSuccessful: true

            echo ('Finished: Packaging and Publishing results')

    }

    stage('Asking for manual approval') {

                input message: 'Go manual approval?', ok: 'Go!'

                echo ('Finished: Asking for manual approval')

    }

    stage('Deployment') {

            sh ("java -jar gradle-simple.jar")

            echo ('Finished: Deployment')

    }

    stage('Sending status') {

            echo ('SUCCESS')

    }

}
