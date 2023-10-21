node {
  def image_name = "vtes-users-image"
  def container_name = "vtes-users"

  stage('checkout') {
    cleanWs()
    checkout scm
  }

  stage('remove old image') {
    try {
      sh "docker stop \$(docker ps -a -q --filter name=${container_name})"
      sh "docker rm \$(docker ps -a -q --filter name=${container_name})"
      sh "docker image prune -fa"
    } catch (Exception e) {
      echo e.getMessage()
    }
  }

  stage('build image') {
   sh "docker build --progress=plain --no-cache -t ${image_name} ."
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