node {
  def postgres_image_name = "vtes-users-db-image"
  def postgres_container_name = "vtes-users-db"
  def keycloak_image_name = "vtes-users-image"
  def keycloak_container_name = "vtes-users"

  stage('checkout') {
    cleanWs()
    checkout scm
  }

  stage('remove old keycloak') {
    try {
      sh "docker compose down --volumes"
      sh "docker stop \$(docker ps -a -q --filter name=${keycloak_container_name})"
      sh "docker rm \$(docker ps -a -q --filter name=${keycloak_container_name})"
      sh "docker image prune -fa"
    } catch (Exception e) {
      echo e.getMessage()
    }
  }

    stage('remove old postgres') {
      try {
        sh "docker stop \$(docker ps -a -q --filter name=${postgres_container_name})"
        sh "docker rm \$(docker ps -a -q --filter name=${postgres_container_name})"
        sh "docker image prune -fa"
      } catch (Exception e) {
        echo e.getMessage()
      }
    }

  stage('deploy') {
    withCredentials([
       string(credentialsId: 'KEYCLOAK_DB', variable: 'KEYCLOAK_DB'),
       string(credentialsId: 'KEYCLOAK_DB_SCHEMA', variable: 'KEYCLOAK_DB_SCHEMA'),
       string(credentialsId: 'KEYCLOAK_DB_USER', variable: 'KEYCLOAK_DB_USER'),
       string(credentialsId: 'KEYCLOAK_DB_PASSWORD', variable: 'KEYCLOAK_DB_PASSWORD'),
       string(credentialsId: 'KEYCLOAK_USER', variable: 'KEYCLOAK_USER'),
       string(credentialsId: 'KEYCLOAK_PASSWORD', variable: 'KEYCLOAK_PASSWORD')
    ]) {
        sh "KEYCLOAK_DB=${KEYCLOAK_DB} KEYCLOAK_DB_SCHEMA=${KEYCLOAK_DB_SCHEMA} KEYCLOAK_DB_USER=${KEYCLOAK_DB_USER} KEYCLOAK_DB_PASSWORD=${KEYCLOAK_DB_PASSWORD} KEYCLOAK_USER=${KEYCLOAK_USER} KEYCLOAK_PASSWORD=${KEYCLOAK_PASSWORD} docker compose up -d --force-recreate"
    }
  }

  stage('clean images') {
    sh "docker image prune -fa"
  }
}