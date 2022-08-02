pipeline {

    agent any
    
    
environment {
        PATH = "/home/admin1/Downloads/apictl:$PATH"
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
                git branch: "master",
                url: 'https://github.com/syndafhal/ci-cd-source.git'
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
                    apictl add env dev --apim https://localhost:9443 
                else
                    echo "Environments :"$envs
                    if [[ $envs != *"dev"* ]]; then
                    echo "Dev environment is not configured. Setting dev environment.."
                    apictl add env dev --apim https://localhost:9443 
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
                mkdir -p upload
                if [ -z "$apis" ]; 
                then 
                    echo "======== No API Changes detected =========="; 
                else 
                    echo "Updated APIs :"$apis
                    apiArray=($apis)
                    for i in "${apiArray[@]}"
                    do
                        echo "$i"
                        apictl bundle -s $i -d upload
                        # get the artifact deploy version from the meta.yaml
                        versionFull=$(cat $i/meta.yaml)
                        versionId=(${versionFull//: / })
                        version=${versionId[1]}
                        for file in upload/*; do
                            echo "Uploading "$file
                            curl -u synda.fhal@gmail.com:Synda_fhal8 -X PUT https://server22/artifactory/myrepo/$i/$version/ -T $file
                        done
                        rm -rf upload
                    done
                fi
                '''
            }
        }

        stage('Update local repo') {
            steps {
                sh '''#!/bin/bash
                idFull=$(cat vcs.yaml)
                arrId=(${idFull//: / })
                repoId=${arrId[1]}
                head=$(git rev-parse HEAD)
                rm /var/lib/jenkins/workspace/gitconfig
                echo "
repos:
  $repoId:
    environments:
       dev:
        lastAttemptedRev: $head" >> /var/lib/jenkins/workspace/gitconfig
                '''
            }
        }
    }   
}
