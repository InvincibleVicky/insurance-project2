pipeline {
    agent any
    
    stages {
        stage("Git Checkout") {
            steps {
                echo "Cloning Git Repo"
                git url: "https://github.com/InvincibleVicky/insurance-project2/"
            }
        }

        stage("Compile the Code") {
            steps {
                echo "Starting Compilation"
                sh "mvn compile"
            }
        }

        stage("Test the Code") {
            steps {
                echo "Starting Testing"
                sh "mvn test"
            }
        }

        stage("QA of the Code") {
            steps {
                echo "Starting Compilation"
                sh "mvn checkstyle:checkstyle"
            }
        }

        stage("Create a Package") {
            steps {
                echo "Packaging Application"
                sh "mvn package"
            }
        }
        
        stage('Publish the HTML Reports') {
             steps {
              publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, 
                       reportDir: '/var/lib/jenkins/workspace/insure-me/target/surefire-reports',
                       reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', 
                       useWrapperFileDirectly: true])
            }
        }

        stage('Create a Docker image from the Package Insure-Me.jar file') {
            steps {
                sh 'docker build -t vigneshwar1908/insure-me:v2 .'
            }
        }
        
        stage('Login to Dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerlogin', passwordVariable: 'dockerpass', usernameVariable: 'dockeruser')]) {
                sh 'docker login -u ${dockeruser} -p ${dockerpass}'                                                       }
            }
        }
        
        stage('Push the Docker image') {
            steps {
                sh 'docker push vigneshwar1908/insure-me:v2'
            }
        }

        stage('Ansbile config and Deployment') {
            steps {
                ansiblePlaybook credentialsId: 'ansible-ssh',
                    disableHostKeyChecking: true,
                    installation: 'ansible',
                    inventory: '/etc/ansible/hosts', 
                    playbook: 'ansible-playbook.yml', vaultTmpPath: ''
            }
        }

    }
}
