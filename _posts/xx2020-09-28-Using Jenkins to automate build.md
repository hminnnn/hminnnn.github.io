---
layout: post
author: HM
---

My builds and deployments are now automated with Jenkins :')


## Create Pipeline 
1. Add Item. Select Pipeline
2. Under Pipeline, select Pipeline script from SCM
- SCM: Select Git and enter your repo details. 
- Script Path: the path to jenkinsfile ("myapp/Scripts/jenkinsfile")
3. Save! 

At the beginning of the pipeline, the directory jenkins is in is `/var/lib/jenkins/workspace/myapp`

From jenkins workspace, I will move my application folder to `/var/www` because that is where I need it to be. 

My folder structure:
```
myapp
|
+-- myapp
|  |  
|  +-- Scripts
|  |
|  |   +-- jenkinsfile  
```

My jenkinsfile:
```
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'lets begin!!!'
                dir('myapp') {
                    sh 'pwd'
                    echo 'Building..'
                    sh 'dotnet publish'
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Testing.. we currently have no tests'
                echo "Running build id: ${env.BUILD_ID}, build no: ${env.BUILD_NUMBER}, 
                        jenkins url: ${env.JENKINS_URL}, job name: ${env.JOB_NAME}"
            }
        }
        stage('Deploy') {
            steps {
                echo 'Transporting files over....'
                echo 'remove backup folder, then move current to backup folder.'
                sh 'sudo rm -rf /var/www/myapp-backup'
                sh 'sudo mv /var/www/myapp/myapp /var/www/myapp-backup'
                echo 'move myapp folder from Jenkins workspace to var/www/myapp'
                sh 'sudo mv myapp /var/www/myapp'
                sh 'ls -la'
                echo 'Deploying....'
                sh 'sudo systemctl restart myapp.service'
                sh 'sudo systemctl status myapp.service'
            }
        }
    }

    post {
        failure {
            echo 'failed'
        }
    }
}
```

Manually trigger the pipeline with Build Now



Error: java.lang.NoSuchMethodError: No such DSL method 'pipeline' found among steps
- Install the Pipeline plugin. It might be missing.


---

## Trigger a build on Jenkins when a push is made on bitbucket

### Jenkins

When configuring pipeline under Build Triggers, check  `Build when a change is pushed to BitBucket` 

Plugins needed: 
- Bitbucket plugin


### Bitbucket
Add webhook
- 8080 because jenkins is on port 8080.


---

## Create Freestyle Project

Under Build, I clicked add build step -> Execute Shell and placed my script (which is almost entirely similar to the Deploy steps in the pipeline) there. 

