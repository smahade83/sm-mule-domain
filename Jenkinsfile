pipeline {

  agent any
  tools {
    maven 'Maven 3.8.6'
    jdk 'jdk8'
  }
  
  environment {
    APP_NAME = readMavenPom().getArtifactId()
    ARTIFACT_ID = readMavenPom().getArtifactId()
    GROUP_ID = readMavenPom().getGroupId()
    VERSION = readMavenPom().getVersion()
    //adding a comment for the commit test
    SCM_URL = scm.getUserRemoteConfigs()[0].getUrl()
    BRANCH = "${GIT_BRANCH.split("/")[1]}"
    RELEASE_NUMBER = "${BRANCH == 'release' ? "${GIT_BRANCH.split("/")[2]}" : ''}"
    RELEASE_BRANCH_NAME = "${BRANCH}" + "/" + "${RELEASE_NUMBER}"
    DEVELOP_BRANCH = "main"
    RELEASE_BRANCH = "release"
    BRANCH_NAME = "${BRANCH == 'release' ? "${RELEASE_BRANCH_NAME}" : 'main'}"

    DEPLOY_CREDS = credentials('deploy-anypoint-user')
    MULE_VERSION = '4.4.0'
    BG = "bd15e991-e67b-4eb9-9f41-7682ab63b4eb"
    WORKER = "Micro"
    M2SETTINGS = "/Users/smahadevan/.m2"
    
  }

  stages {
    stage('Build') {
      
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
          sh 'echo ${BRANCH_NAME}'
          checkout poll: false, scm: [$class: 'GitSCM', branches: [[name: "*/${BRANCH_NAME}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'Github', url: "${SCM_URL}"]]]
          sh 'mvn clean -DskipTests package'
        }
      }
    }
    stage('check file') {
      steps {
        script {
          if (fileExists("${WORKSPACE}/target/${APP_NAME}-${VERSION}-mule-application.jar")) {
            echo "${APP_NAME}-${VERSION}-mule-application.jar found!"
          }else{
            echo "${WORKSPACE}/target/${APP_NAME}-${VERSION}-mule-application.jar"
            echo "${APP_NAME}-${VERSION}-mule-application.jar not found!"
          }
        }
      }
    }
  }
}