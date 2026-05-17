@Library('JenkinsPipelines') _ // See https://github.com/mwdle/JenkinsPipelines

def isMainBranch = env.BRANCH_NAME == 'main'

// Only set a schedule if we are on main
def schedule = isMainBranch ? 'H H/6 * * *' : null

// Only define the Webhook trigger if we are on main
def branchTriggers = []
if (isMainBranch) {
    branchTriggers.add([
        $class: 'GenericTrigger',
        causeString: 'Triggered by Git server webhook',
        genericVariables: [
            [defaultValue: '', key: 'GIT_REPO', regexpFilter: '', value: '$.repository.full_name'],
            [defaultValue: '', key: 'GIT_SENDER', regexpFilter: '', value: '$.sender.login']
        ],
        regexpFilterExpression: '^(?!renovatebot$).*',
        regexpFilterText: '$GIT_SENDER',
        silentResponse: true,
        token: '', tokenCredentialId: 'Renovate Webhook Token'
    ])
}

dockerComposePipeline(
    envFileCredentialIds: ['common.env', 'renovate-config.env'],
    defaultDetached: false,
    alertEmail: "${env.ALERT_EMAIL}",
    disableIndexTriggers: true,
    cronSchedule: schedule,
    additionalTriggers: branchTriggers
)
