pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "AhmedBen98/flask-devops-app"
        DOCKER_TAG = "latest"
        CONTAINER_NAME = "flask-devops-app"
        APP_PORT = "5000"
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Clonage du dépôt Git..."
                git url: 'https://github.com/AhmedBen98/flask-devops-app.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Construction de l'image Docker..."
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Stop and Remove old container if exists') {
            steps {
                echo "Arrêt et suppression du container précédent si existant..."
                script {
                    sh """
                    if [ \$(docker ps -aq -f name=${CONTAINER_NAME}) ]; then
                      docker stop ${CONTAINER_NAME} || true
                      docker rm ${CONTAINER_NAME} || true
                    else
                      echo "Aucun container existant à supprimer."
                    fi
                    """
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                echo "Lancement du container Docker..."
                script {
                    sh """
                    docker run -d -p ${APP_PORT}:${APP_PORT} --name ${CONTAINER_NAME} ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                    echo " Application Flask lancée sur : http://localhost:${APP_PORT} (ou l'adresse IP de Jenkins)"
                }
            }
        }

        stage('Test Application') {
            steps {
                echo "Vérification que le container tourne..."
                script {
                    sh "docker ps -f name=${CONTAINER_NAME}"
                    sh """
                    echo " Test de l'application Flask sur http://localhost:${APP_PORT}"
                    sleep 5
                    curl -f http://localhost:${APP_PORT} || (echo ' L’application ne répond pas !' && exit 1)
                    """
                }
            }
        }
    }

    post {
        always {
            echo " Pipeline terminée"
        }
    }
}
