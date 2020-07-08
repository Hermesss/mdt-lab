pipeline{
    
    agent {
        label 'docker'
        }

    stages{
        stage('checkout'){
            steps{
                git credentialsId: 'student10-github', url: 'https://github.com/Hermesss/mdt-lab.git'
            }
        }
        stage (' Test ') {
            
        when{
               branch 'PR-*'  
            } 
        environment {
        scannerHome = tool 'sonarscanner4'
          }   
    
            steps {
            
            withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner\
              -Dsonar.projectKey=student10-project \
              -Dsonar.sources=. \
              -Dsonar.host.url=http://server1.jenkins-practice.tk:9000 \
              -Dsonar.login=3dffccf3f42cfc67d1e33d664e1cfc8a6e30d5fc"
                 }
        timeout(time: 3, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
                    }
            }

        }
        
        stage('Build'){
            tools {nodejs "Node12"}
            when{
                branch 'master'
               } 
            steps {
                echo 'run this stage - ony if the branch = master branch'
                sh ''' npm install -g uglify-js npm install clean-css-cli -g '''
                sh ''' cd ./www/js
                       uglifyjs init.js -o init.min.js
                       cd ../css
                       cleancss -o style.min.css style.css
                       cd ../..
                       echo $PWD
                       tar -czvf www.tar.gz www
                   '''
                   }              
        }
        stage("Archive"){
            steps{
            nexusArtifactUploader artifacts: [[artifactId: 'www', classifier: '', file: 'www.tar.gz', type: 'tar.gz']], credentialsId: 'student10-jenkins', groupId: 'student10', nexusUrl: 'https://server2.jenkins-practice.tk/', nexusVersion: 'nexus3', protocol: 'http', repository: 'student10-repo', version: '${BASE_VERSION}-${BUILD_NUMBER}'            }
                    }      
        }   
        stage("Deploy"){
            steps{
                build 'Deploy'
                }       
            }
}

