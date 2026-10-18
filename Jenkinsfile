String cronSchedule = env.BRANCH_NAME == 'main' ? 'H H * * *' : ''

pipeline {
    agent {
        label 'podman'
    }

    options {
        disableConcurrentBuilds()
        overrideIndexTriggers(false)
    }

    triggers {
        cron(cronSchedule)
        GenericTrigger(
            causeString: 'Triggered by Git server webhook',
            genericVariables: [
                [key: 'GIT_REPO', regexpFilter: '', value: '$.repository.full_name'],
                [key: 'GIT_SENDER', regexpFilter: '', value: '$.sender.login']
            ],
            regexpFilterExpression: '^main (?!renovatebot$).*',
            regexpFilterText: env.BRANCH_NAME + ' $GIT_SENDER',
            silentResponse: true,
            token: '',
            tokenCredentialId: 'renovate-webhook-token'
        )
    }

    stages {
        stage('Run Renovate') {
            steps {
                withCredentials([
                    string(credentialsId: 'renovate-token', variable: 'RENOVATE_TOKEN'),
                    string(credentialsId: 'renovate-github-com-token', variable: 'RENOVATE_GITHUB_COM_TOKEN')
                ]) {
                    sh '''
                        podman-remote run --rm \
                            --read-only \
                            --tmpfs /tmp \
                            --volume renovate-cache:/tmp/renovate/cache:Z \
                            --volume renovate-repos:/tmp/renovate/repos:Z \
                            --userns=nomap \
                            --security-opt no-new-privileges \
                            --cap-drop ALL \
                            --cpus 1.5 \
                            --memory 2g \
                            --pids-limit 50 \
                            --env RENOVATE_PLATFORM=forgejo \
                            --env RENOVATE_ENDPOINT=https://git.mwdle.com/api/v1 \
                            --env RENOVATE_AUTODISCOVER=true \
                            --env "RENOVATE_AUTODISCOVER_FILTER=${GIT_REPO:-lab/*}" \
                            --env 'RENOVATE_ONBOARDING_CONFIG={"extends":["local>lab/renovate-config"]}' \
                            --env "RENOVATE_TIMEZONE=${TZ}" \
                            --env RENOVATE_TOKEN \
                            --env RENOVATE_GITHUB_COM_TOKEN \
                            docker.io/renovate/renovate:44.167-full@sha256:9d90dc211443bc0c7a821198fbd0e7312e094bec058eb55f23508233d7edb179
                    '''
                }
            }
        }
    }

    post {
        failure {
            script {
                if (env.ALERT_EMAIL?.trim()) {
                    mail(
                        to: env.ALERT_EMAIL,
                        subject: "🚨 Build Failure - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: "Build failed!\n\nJob: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}\n\nCheck Jenkins logs: ${env.BUILD_URL}"
                    )
                }
            }
        }
        cleanup {
            cleanWs(deleteDirs: true, disableDeferredWipeout: true)
        }
    }
}
