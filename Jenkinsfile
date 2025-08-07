pipeline {
  agent any

  environment {
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
          def POD_NAME = sh(
            script: "kubectl get pods -l app=wordpress -o jsonpath={.items[0].metadata.name}",
            returnStdout: true
          ).trim()

          sh "kubectl exec ${POD_NAME} -- rm -rf $DEST_PATH/themes/*"
          sh "kubectl exec ${POD_NAME} -- rm -rf $DEST_PATH/plugins/*"
          sh "kubectl exec ${POD_NAME} -- rm -rf $DEST_PATH/uploads/*"

          sh "kubectl cp wp-content/themes ${POD_NAME}:$DEST_PATH/themes"
          sh "kubectl cp wp-content/plugins ${POD_NAME}:$DEST_PATH/plugins"
          sh "kubectl cp wp-content/uploads ${POD_NAME}:$DEST_PATH/uploads"

          // Optionnel : vérification que le fichier theme.json est bien copié
          sh "kubectl exec ${POD_NAME} -- cat $DEST_PATH/themes/twentytwentythree/theme.json"
        }
      }
    }
  }
}
