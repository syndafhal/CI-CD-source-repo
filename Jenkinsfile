pipeline {

    agent any
    
    
environment {
        PATH = "/home/synda/Bureau/apictl:$PATH"
    }


    stages {
        
        stage('Preparation') {
            steps{
                git branch: "main",
                url: 'https://github.com/syndafhal/Deployment.git'

            }
        }

        stage('Setup Environment for APICTL') {
            steps {
                sh '''#!/bin/bash
                #rm /var/lib/jenkins/workspace/gitconfig
                #touch /var/lib/jenkins/workspace/gitconfig
                apictl set --vcs-config-path /var/lib/jenkins/workspace/gitconfig
                apictl remove env dev
                envs=$(apictl get envs --format "{{.Name}}")
                if [ -z "$envs" ]; 
                then 
                    echo "No environmen configured Setting dev environment.."
                    apictl add env dev --apim https://10.1.31.163:9443 
                else
                    echo "Environments :"$envs
                    if [[ $envs != *"dev"* ]]; then
                    echo "Dev environment is not configured. Setting dev environment.."
                    apictl add env dev --apim https://10.1.31.163:9443
                    fi
                fi
                apictl get envs
                '''
            }
        }
          stage('Deploy to Development Environment') {

            steps {
         sh '''#!/bin/bash
         declare -i x=0
          
         

        # download the artifact from the artifact repository
        apictl login dev -u admin -p admin -k
        apis=$(apictl vcs status -e dev --format="{{ jsonPretty . }}" | jq -r '.API | .[] | .NickName')
        apiArray=($apis)
        for i in "${apiArray[@]}"
                    do
                        echo "$i"
        done      
        versionFull=$(cat $i/meta.yaml)
        echo $versionFull
        versionId=(${versionFull//: / })
        echo $versionId
        echo ok
        version=${versionId[1]}
        echo $version
        cd '/var/lib/jenkins/workspace/CI-CD Dev Deploy'
        wget https://server2.jfrog.io/artifactory/repo/PizzaShackAPI-1.0.0/$version/PizzaShackAPI_1.0.0.zip 
        unzip PizzaShackAPI_1.0.0.zip 
        message=$(apictl import api -f PizzaShackAPI-1.0.0 --params DeploymentArtifacts_PizzaShackAPI-1.0.0 -e dev --update -k)
        if [ "$message" = "Successfully imported API." ]; then
            echo "Successfully imported API."
        else
            echo $message
        fi
        x=$((x+1))
        echo $x
        
        
        
        
       
        
        
        
       


        
        
        
        
        
        

     
      
       
        '''
                
       
      

      


      

        

            }
        }
       
    }}
