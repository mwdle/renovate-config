pipeline {
    agent { label 'docker' }
    options { disableConcurrentBuilds() }

    triggers {
        cron('@hourly')
        GenericTrigger(
            causeString: 'Triggered by Git server webhook',
            genericVariables: [
                [defaultValue: '', key: 'GIT_REPO', regexpFilter: '', value: '$.repository.full_name'],
                [defaultValue: '', key: 'GIT_SENDER', regexpFilter: '', value: '$.sender.login']
            ],
            printContributedVariables: true,
            regexpFilterExpression: '^(?!renovatebot$).*',
            regexpFilterText: '$GIT_SENDER',
            silentResponse: true,
            token: '', tokenCredentialId: 'Renovate Webhook Token'
        )
    }

    stages {
        stage('Run Renovate') {
            steps {
                withCredentials([file(credentialsId: 'renovate-config.env', variable: 'COMPOSE_ENV')]) {
                    sh 'docker compose --env-file $COMPOSE_ENV up --abort-on-container-exit'
                }
            }
        }
    }

    post {
        failure {
            mail to: "${env.ALERT_EMAIL}",
                 subject: "🚨 Renovate Bot Failure - Build #${env.BUILD_NUMBER}",
                 body: "Renovate Bot failed!\n\nCheck Jenkins logs here: ${env.BUILD_URL}"
        }
    }
}
