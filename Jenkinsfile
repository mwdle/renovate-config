@Library('JenkinsPipelines') _ // See https://github.com/mwdle/JenkinsPipelines

dockerComposePipeline(
    envFileCredentialIds: ['renovate-config.env'],
    disableConcurrentBuilds: true,
    defaultDetached: false,
    alertEmail: "${env.ALERT_EMAIL}",
    disableIndexTriggers: true,
    quietPeriod: 0,
    cronSchedule: '@hourly',
    additionalTriggers: [
        // Requires Jenkins Controller to have Generic Webhook Trigger plugin installed
        [$class: 'GenericTrigger',
            causeString: 'Triggered by Git server webhook',
            genericVariables: [
                [defaultValue: '', key: 'GIT_REPO', regexpFilter: '', value: '$.repository.full_name'],
                [defaultValue: '', key: 'GIT_SENDER', regexpFilter: '', value: '$.sender.login']
            ],
            regexpFilterExpression: '^(?!renovatebot$).*',
            regexpFilterText: '$GIT_SENDER',
            silentResponse: true,
            token: '', tokenCredentialId: 'Renovate Webhook Token'
        ]
    ]
)
