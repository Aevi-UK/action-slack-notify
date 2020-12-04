This is a fork of [rtCamp/action-slack-notify](https://github.com/rtCamp/action-slack-notify) with modifications to support AEVI use cases that were not possible without code changes.

Newly created Slack apps (as of Oct 2020) no longer accepts setting app related metadata like app name, icon etc via the API. Hence, `SLACK_USERNAME`, `SLACK_ICON` etc are no longer supported. Instead, these need to configured in the Slack app itself.

There are currently two channels set up with webhooks:
- `sdk-releases` for Android app releases, use secret: `SLACK_WEBHOOK_SDK_RELEASES`
- `cloud-deployments` for AWS releases/deployments, use secret: `SLACK_WEBHOOK_CLOUD_DEPLOYMENTS`

## Supported fields

Variable|Description
|----|----
`SLACK_CHANNEL`| The channel to post to. Must match the webhook configuration.
`SLACK_WEBHOOK`| The webhook associated with a Slack app that allows posting to a channel
`SLACK_COLOR`| The hex color to use for the vertical line
`SLACK_PRETEXT`| The text to show above the message content (basically the title)
`SLACK_FOOTER`| The footer text
`VERSION_NAME`| The version associated with the release/deploymnent. Not required where the workflow is triggered by a tag.
`VARIANTS`| The list of binary variants produced. In the case of Android, this is `release`, `unsigned,` etc
`ENVIRONMENTS`| The environment(s) deployed to.
`BASE_URL`| The base URL(s) of the environments(s)
`CHANGELOG_URL`| The URL to the `CHANGELOG.md` in the repo
`RELEASE_URL`| The URL to the Github release page

## Examples

### Android APK releases
```
- name: Slack Notification
  uses: Aevi-UK/action-slack-notify@master
  env:
    SLACK_CHANNEL: sdk-releases
    SLACK_COLOR: '#CC0033'
    SLACK_PRETEXT: New Android Release
    VERSION_NAME: 1.0.0 // Should be set from the tag
    VARIANTS: release, unsigned // Should be set dynamically
    CHANGELOG_URL: https://github.com/Aevi-UK/<repo>/blob/<branch/tag>/CHANGELOG.md
    RELEASE_URL: ${{ steps.create_release.outputs.html_url }} // From create-release action
    SLACK_FOOTER: Some nonsense.
    SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK_SDK_RELEASES }}  
```          

### Web release
```
- name: Slack Notification
  uses: Aevi-UK/action-slack-notify@master
  env:
    SLACK_CHANNEL: cloud-deployments
    SLACK_COLOR: '#CC0033'
    SLACK_PRETEXT: New <thingie> release
    CHANGELOG_URL: https://github.com/Aevi-UK/<repo>/blob/<branch/tag>/CHANGELOG.md
    RELEASES_URL: ${{ steps.create_release.outputs.html_url }}
    SLACK_FOOTER: Some nonsense.
    SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK_CLOUD_DEPLOYMENTS }}
```

### Web deployment
```
- name: Slack Notification
  if: startsWith(github.ref, 'refs/heads/deploy') // Limit to some pattern
  uses: Aevi-UK/action-slack-notify@master
  env:
    SLACK_CHANNEL: cloud-deployments
    SLACK_COLOR: '#CC0033'
    SLACK_PRETEXT: <thingie> deployment
    VERSION_NAME: ${{env.VERSION}}
    ENVIRONMENT: ${{env.STAGE}}
    BASE_URL: https://cloudflow-${{env.STAGE}}.aevi-test.io/v1
    SLACK_FOOTER: Some nonsense.
    SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK_CLOUD_DEPLOYMENTS }}
```
