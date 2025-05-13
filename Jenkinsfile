pipeline {
  agent any

  environment {
    IMAGE_NAME = 'chiomanwanedo/devsecops-app'
    GITHUB_CREDENTIAL_ID = 'github'
  }

  stages {
    stage('Checkout GitOps Repo') {
      steps {
        git url: 'https://github.com/chiomanwanedo/DevSecOps-Project-CD.git', 
            branch: 'main', 
            credentialsId: GITHUB_CREDENTIAL_ID
      }
    }

    stage('Download Image Tag from CI') {
      steps {
        copyArtifacts(
          projectName: 'DevSecOps-Project',
          selector: lastSuccessful(),
          filter: 'image-tag.txt',
          target: '.'
        )
        sh 'cat image-tag.txt' // Debug: See the tag in the logs
      }
    }

    stage('Update Kubernetes Manifest') {
      steps {
        script {
          def tag = readFile('image-tag.txt').trim()
          sh """
            sed -i 's|image: ${IMAGE_NAME}:.*|image: ${IMAGE_NAME}:${tag}|' kubernetes/deployment.yml
          """
        }
      }
    }

    stage('Commit & Push Changes') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: GITHUB_CREDENTIAL_ID,
          usernameVariable: 'GIT_USER',
          passwordVariable: 'GIT_PASS'
        )]) {
          sh '''
            git config user.name "chiomanwanedo"
            git config user.email "chiomavanessa8@gmail.com"

            # Reattach to main branch in case Jenkins detached HEAD
            git checkout -B main

            # Stage and commit the new image tag and manifest update
            git add image-tag.txt kubernetes/deployment.yml
            git commit -m "Update image tag for deployment"

            # Push back to GitHub using authenticated URL
            git push https://$GIT_USER:$GIT_PASS@github.com/chiomanwanedo/DevSecOps-Project-CD.git main
          '''
        }
      }
    }

    // Optional: If you want to trigger ArgoCD manually from Jenkins
    // stage('Sync with ArgoCD') {
    //   steps {
    //     sh 'argocd app sync your-app-name --insecure --grpc-web'
    //   }
    // }
  }

  post {
    success {
      echo "✅ CD pipeline completed successfully and changes pushed to GitHub."
    }
    failure {
      echo "❌ CD pipeline failed. Check the logs above for the cause."
    }
  }
}
