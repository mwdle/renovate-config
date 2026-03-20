@Library('JenkinsPipelines') _

/*
 * This Docker Compose deployment is managed by the `dockerComposePipeline` defined in the
 * Jenkins Pipelines shared library (https://github.com/mwdle/JenkinsPipelines).
 *
 * Configuration:
 * - envFileCredentialIds:
 *   Injects secrets from a Jenkins 'Secret file' credential. It expects the credential ID
 *   to match the name of this repository, suffixed with '.env'.
 *
 * - cronSchedule:
 *   Defines when the pipeline should be triggered automatically using Jenkins cron syntax.
 *   The format is: MINUTE HOUR DAY_OF_MONTH MONTH DAY_OF_WEEK
 *   Example: 'H 6,18 * * *' runs the job twice daily (around 6 AM and 6 PM),
 *   where 'H' distributes load by picking a consistent hashed minute.
 *
 * - defaultDetached:
 *   Controls whether services run in the background (detached mode).
 *   Set to `false` here so Renovate runs in the foreground, streaming logs directly
 *   to the build output and propagating its exit code to Jenkins.
 *
 */
dockerComposePipeline(
    envFileCredentialIds: ['renovate-config.env'],
    cronSchedule: 'H 6,18 * * *',
    defaultDetached: false
)
