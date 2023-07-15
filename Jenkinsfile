node{
    
    def mavenHome
    def mavenCMD
    def docker
    def dockerCMD
    def tagName
    
    stage('prepare enviroment'){
        echo 'initialize all the variables'
        mavenHome = tool name: 'maven' , type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
        docker = tool name: 'docker' , type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
        dockerCMD = "${docker}/bin/docker"
        tagName="3.0"
    }
    
    stage('git code checkout'){
        try{
            echo 'checkout the code from git repository'
            git "https://github.com/Tejaswy22/Lahansvika.git"
        }
        catch(Exception e){
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
            emailext body: '''Dear All,
            The Jenkins job ${JOB_NAME} has been failed. Request you to please have a look at it immediately by clicking on the below link. 
            ${BUILD_URL}''', subject: 'Job ${JOB_NAME} ${BUILD_NUMBER} is failed', to: 'shubham@gmail.com'
        }
    }
    
    stage('Build the Application'){
        echo "Cleaning... Compiling...Testing... Packaging..."
        //sh 'mvn clean package'
        sh "${mavenCMD} clean package"        
    }
    
    stage('publish test reports'), reportDir: '/var/lib/jenkins/workspace/Insureme/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
    }
    
    stage('Containerize the application'){
        echo 'Creating Docker image'
        sh "${dockerCMD} build -t tejalucky/insureme2:1.0 ."
    }
    
    stage('Pushing it ot the DockerHub'){
        echo 'Pushing the docker image to DockerHub'
        withCredentials([string(credentialsId: 'dock-password', variable: 'dockerHubPassword')]) {
        sh "${dockerCMD} login -u tejalucky -p Reshvanth@2407"
        sh "${dockerCMD} push tejalucky/insureme2:1.0"
            
        }
        
    stage('Configure and Deploy to the test-server'){
        ansiblePlaybook become: true, credentialsId: 'tejareshu', disableHostKeyChecking: true, installation: 'Ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml'
    }
        
        
    }
}




