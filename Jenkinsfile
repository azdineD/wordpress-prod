pipeline {
    agent any

    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'master', url: 'https://github.com/azdineD/wordpress-prod.git'
            }
        }

        stage('Déployer les fichiers WordPress') {
            steps {
                echo "Copie des fichiers vers le pod Kubernetes..."
                sh '''
                POD=$(kubectl get pod -n default -l app=wordpress -o jsonpath="{.items[0].metadata.name}")
                kubectl cp wp-content $POD:/var/www/html/wp-content -n default
                '''
            }
        }

        stage('Fin') {
            steps {
                echo 'Déploiement terminé !'
            }
        }
    }
}
