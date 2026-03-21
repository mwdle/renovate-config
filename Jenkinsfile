@Library('JenkinsPipelines') _

/*
 * This Docker Compose deployment is managed by the `dockerComposePipeline` defined in the
 * Jenkins Pipelines shared library (https://github.com/mwdle/JenkinsPipelines).
 */
dockerComposePipeline(
    envFileCredentialIds: ['renovate-config.env'],
    disableConcurrentBuilds: true,
    defaultDetached: false,
    alertEmail: "${env.ALERT_EMAIL}",
    disableIndexTriggers: true,
    cronSchedule: '@hourly',
    additionalTriggers: [
        [$class: 'GenericTrigger',
            causeString: 'Triggered by Git server webhook',
            genericVariables: [
                [defaultValue: '', key: 'GIT_REPO', regexpFilter: '', value: '$.repository.full_name'],
                [defaultValue: '', key: 'GIT_SENDER', regexpFilter: '', value: '$.sender.login']
            ],
            regexpFilterExpression: '^(?!renovatebot$).*',
            regexpFilterText: '$GIT_SENDER',
            token: '', tokenCredentialId: 'Renovate Webhook Token'
        ]
    ]
)
