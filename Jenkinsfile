pipeline {
    agent any

    environment {
        ENV_NAME = "wso2-local"
        API_PROJECT_PATH = "${env.WORKSPACE}"
        APIM_HOST = "https://192.168.8.100:9446"
        HOME = "${env.WORKSPACE}"
        CONFIG_FILE = "${env.WORKSPACE}/.wso2apictl/main_config.yaml"
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    def configFileExists = fileExists(CONFIG_FILE)
                    def envExists = false

                    if (configFileExists) {
                        def grepCmd = "grep -q ' ${ENV_NAME}:' ${CONFIG_FILE}"
                        def result = sh(script: grepCmd, returnStatus: true)
                        envExists = (result == 0)
                    }

                    if (!envExists) {
                        echo "Adding environment ${ENV_NAME}"
                        sh "apictl add env ${ENV_NAME} --apim ${APIM_HOST}"
                    } else {
                        echo "Environment ${ENV_NAME} already exists"
                    }

                    echo "Logging in to ${ENV_NAME}"
                    sh "apictl login ${ENV_NAME} -u admin -p admin -k"
                }
            }
        }

        stage('Import API') {
            steps {
                sh "apictl import api -f ${API_PROJECT_PATH} -e ${ENV_NAME} -k --update"
            }
        }
    }
}
