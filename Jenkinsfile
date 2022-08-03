pipeline {

    agent any
    
    
environment {
        PATH = "/home/synda/Bureau/apictl:$PATH"
    }
    options {
        buildDiscarder logRotator( 
                    daysToKeepStr: '16', 
                    numToKeepStr: '10'
            )
    }

    stages {
        
        stage('Preparation') {
            steps{
                git branch: "main",
                url: 'https://github.com/syndafhal/pipeline-source.git'
            }
        }

        stage('Setup Environment for APICTL') {
            steps {
                sh '''#!/bin/bash
                #rm /var/lib/jenkins/workspace/gitconfig
                #touch /var/lib/jenkins/workspace/gitconfig
                apictl set --vcs-config-path /var/lib/jenkins/workspace/gitconfig
                envs=$(apictl get envs --format "{{.Name}}")
                if [ -z "$envs" ]; 
                then 
                    echo "No environment configured. Setting dev environment.."
                    apictl add env dev --apim https://10.1.31.188:9443 
                else
                    echo "Environments :"$envs10.1.31.18810.1.31.188
                    if [[ $envs != *"dev"* ]]; then
                    echo "Dev environment is not configured. Setting dev environment.."
                    apictl add env dev --apim https://10.1.31.188:9443 
                    fi
                fi
                '''
            }
        }
        
 stage('Build api bundles and upload') {
            steps {
                sh '''#!/bin/bash
                apictl login dev -u admin -p admin -k
                apis=$(apictl vcs status -e dev --format="{{ jsonPretty . }}" | jq -r '.API | .[] | .NickName')
                
             
                '''
            }
        }
    }}
