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

                    // Update deployment YAML in kubernetes folder
                    dir("kubernetes") {
                        sh """
                            sed -i 's|image:.*|image: ngozin/devsecops-project:${params.IMAGETAG}|' deployment.yml
                        """
                    }

                    // Configure Git
                    sh '''
                        git config user.email "jenkins@example.com"
                        git config user.name "Jenkins"
                    '''

                    // Commit and push changes
                    sh "git commit -am \"New deployment for Build ${params.IMAGETAG}\""
                    sh """git push https://x-access-token:${params.PASSWD}@github.com/Ngozi-N/DevSecOps-Project-CD.git"""
                }
            }
        }
    }
}
