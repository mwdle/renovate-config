pipeline {
    agent none

    options {
        disableConcurrentBuilds()
        overrideIndexTriggers(false)
    }

    triggers {
        cron('H H * * *')
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
            agent {
                label 'podman'
            }
            when {
                beforeAgent true
                branch "main"
            }
            steps {
                withCredentials([
                    string(credentialsId: 'renovate-github-com-token', variable: 'RENOVATE_GITHUB_COM_TOKEN'),
                    string(credentialsId: 'renovate-token', variable: 'RENOVATE_TOKEN'),
                    usernamePassword(credentialsId: 'renovate-docker-pat', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')
                ]) {
                    sh '''
                        export RENOVATE_HOST_RULES='[{"hostType":"docker","matchHost":"docker.io","username":"'"$DOCKER_USERNAME"'","password":"'"$DOCKER_PASSWORD"'"}]'  
                        podman-remote run --rm \
                            --env RENOVATE_AUTODISCOVER=true \
                            --env "RENOVATE_AUTODISCOVER_FILTER=${GIT_REPO:-lab/*}" \
                            --env RENOVATE_ENDPOINT=https://git.mwdle.com/api/v1 \
                            --env RENOVATE_GITHUB_COM_TOKEN \
                            --env RENOVATE_HOST_RULES \
                            --env 'RENOVATE_ONBOARDING_CONFIG={"extends":["local>lab/renovate-config"]}' \
                            --env RENOVATE_PLATFORM=forgejo \
                            --env RENOVATE_TOKEN \
                            --volume /opt/containerbase \
                            --volume renovate-cache:/tmp/renovate/cache:U,Z \
                            --volume renovate-repos:/tmp/renovate/repos:U,Z \
                            --read-only \
                            --tmpfs /tmp:noexec,nosuid \
                            --userns=nomap \
                            --security-opt no-new-privileges \
                            --cap-drop ALL \
                            --memory 2g \
                            --pids-limit 256 \
                            --cpus 1.5 \
                            docker.io/renovate/renovate:44.181.7@sha256:4f5a2621afd6629fda621e5c66221d1000f0ef3330ec14c828d7e8d357f03522
                    '''
                }
            }
            post {
                cleanup {
                    cleanWs(deleteDirs: true, disableDeferredWipeout: true)
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
    }
}