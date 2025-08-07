pipeline {
  agent any

  environment {
    POD_NAME = sh(
      script: "kubectl get pods -l app=wordpress -o jsonpath='{.items[0].metadata.name}'",
      returnStdout: true
    ).trim()
    DEST_PATH = "/var/www/html/wp-content"
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/azdineD/wordpress-prod.git'
      }
    }

    stage('Deploy wp-content') {
      steps {
        script {
          // Supprimer l'ancien contenu
          sh "kubectl exec ${POD_NAME} -- rm -rf ${DEST_PATH}/themes/*"
          sh "kubectl exec ${POD_NAME} -- rm -rf ${DEST_PATH}/plugins/*"
          sh "kubectl exec ${POD_NAME} -- rm -rf ${DEST_PATH}/uploads/*"

          // Copier le thème personnalisé uniquement
          sh "kubectl cp wp-content/themes/twentytwentythree ${POD_NAME}:${DEST_PATH}/themes/twentytwentythree"

          // Copier tous les plugins un par un s'ils existent
          def pluginFiles = sh(script: "ls -1 wp-content/plugins || true", returnStdout: true).trim().split("\n")
          for (plugin in pluginFiles) {
            if (plugin.trim()) {
              sh "kubectl cp wp-content/plugins/${plugin} ${POD_NAME}:${DEST_PATH}/plugins/${plugin}"
            }
          }

          // Copier tous les fichiers uploads un par un
          def uploadFiles = sh(script: "ls -1 wp-content/uploads || true", returnStdout: true).trim().split("\n")
          for (upload in uploadFiles) {
            if (upload.trim()) {
              sh "kubectl cp wp-content/uploads/${upload} ${POD_NAME}:${DEST_PATH}/uploads/${upload}"
            }
          }
        }
      }
    }
  }
}
