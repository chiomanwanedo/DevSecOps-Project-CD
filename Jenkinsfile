pipeline {
    agent any

    parameters {
        password(name: 'PASSWD', defaultValue: '', description: 'Enter your GitHub Personal Access Token')
        string(name: 'IMAGETAG', defaultValue: '1', description: 'Enter the Docker image tag to deploy')
        choice(name: 'environment', choices: ['functional', 'integration', 'regression', 'uat', 'release'], description: 'Select the deployment environment')
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    // Checkout from GitHub
                    git branch: 'main', credentialsId: 'GitHubToken', url: 'https://github.com/Ngozi-N/DevSecOps-Project-CD.git'

                    // Update deployment YAML in the correct environment folder
                    dir("${params.environment}") {
                        sh """
                            sed -i 's|image:.*|image: ngozin/devsecops-project:${params.IMAGETAG}|' deployment.yml
                        """
                    }

                    // Git config for Jenkins to commit
                    sh '''
                        git config user.email "jenkins@example.com"
                        git config user.name "Jenkins"
                    '''

                    // Commit and push the changes back to GitHub
                    sh "git commit -am \"New deployment for Build ${params.IMAGETAG}\""
                    sh "git push https://${params.PASSWD}@github.com/Ngozi-N/DevSecOps-Project-CD.git"
                }
            }
        }
    }
}
