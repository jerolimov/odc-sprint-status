# Jira_status_script
bash script for automatic fetching of JIRA status

## Prerequisite 
- Must have jq installed for running the script from local.
- Must setup JIRA personal access token for querying private filters/boards. Follow steps below.
- Must be able to JIRA REST API. 
- Must be able to GITHUB REST API.
- Must setup incoming webhook for slack channel.

## How to setup
1. You need to obtain Personal Access Token from JIRA by clicking on your profile -> Personal Access Tokens -> Create Token and save this token locally.
2. You need to create a Slack APP for enabling incoming webhooks (create slack app | https://api.slack.com/authentication/basics )
3. Need to enable incoming webhook and set it up against the required channel (incoming webhooks | https://api.slack.com/messaging/webhooks )
4. You need to find the api accesspoint for your jira project and use them in the script accordingly (JIRA REST API | https://developer.atlassian.com/server/jira/platform/rest-apis/)
> eg: For a filter `https://issues.redhat.com/issues/?filter=12396633` the endpoint is usually `https://issues.redhat.com/rest/api/2/search?     jql=filter=12396633` with `api/<version>` being the version number.
5. It is preferable to use filters for JIRA. So save JIRA quieries as filters in your script. Long queries are confusing to manage.
6. You need to find the api accesspoint for your GITHUB project and use them in the script accordingly.
> eg: `https://github.com/search?q=repo%3Aopenshift%2Fhac-dev+repo%3Aopenshift%2Fconsole+repo%3Aopenshift%2Fenhancements+repo%3Aopenshift%2Fconsole-operator+repo%3Aopenshift%2Fapi+-label%3Ado-not-merge%2Fwork-in-progress+-label%3Algtm+author%3Arohitkrai03+author%3Adebsmita1+author%3Ajeff-phillips-18+author%3AinvincibleJai+author%3Anemesis09+author%3Asahil143+author%3Avikram-raj+author%3Achristianvogt+author%3Ajerolimov+author%3AdivyanshiGupta+author%3Arottencandy+author%3Akarthikjeeyar+author%3Aabhinandan13jan+state%3Aopen+created%3A%3C2022-06-19+author%3ALucifergene+author%3Agruselhaus+author%3Alokanandaprabhu&type=Issues&ref=advsearch&l=&l=` `https://api.github.com/search/issues?q=repo%3Aopenshift%2Fhac-dev+repo%3Aopenshift%2Fconsole+repo%3Aopenshift%2Fenhancements+repo%3Aopenshift%2Fconsole-operator+repo%3Aopenshift%2Fapi+-label%3Ado-not-merge%2Fwork-in-progress+-label%3Algtm+author%3Arohitkrai03+author%3Adebsmita1+author%3Ajeff-phillips-18+author%3AinvincibleJai+author%3Anemesis09+author%3Asahil143+author%3Avikram-raj+author%3Achristianvogt+author%3Ajerolimov+author%3AdivyanshiGupta+author%3Arottencandy+author%3Akarthikjeeyar+author%3Aabhinandan13jan+state%3Aopen+created%3A%3C2022-06-19+author%3ALucifergene+author%3Agruselhaus+author%3Alokanandaprabhu&type=repositories`
7. You need to make a CURL response and extract the exact field [eg: `total`] using `jq` or other similar.
8. It is preferred to store individual responses in separate variables and append them accordingly.
9. Once we have separate varables to store separate block we need to create the SLACK message in a recognisable format using <messaging blocks | https://api.slack.com/messaging/composing/layouts> or basic text.
> eg: `{\"type\":\"home\", \"blocks\":[{\"type\":\"header\",\"text\":{\"type\":\"plain_text\",\"text\":\"$head\"}},{\"type\":\"section\",\"fields\":[{\"type\":\"mrkdwn\",\"text\":\"$hac_stories\"},{\"type\":\"mrkdwn\",\"text\":\"$hac_bugs\"}]},{\"type\":\"divider\"},{\"type\":\"header\",\"text\":{\"type\":\"plain_text\",\"text\":\"ODC\"}},{\"type\":\"section\",\"fields\":[{\"type\":\"mrkdwn\",\"text\":\"$odc_stories\"},{\"type\":\"mrkdwn\",\"text\":\"$odc_bugs\"}]},{\"type\":\"divider\"},{\"type\":\"header\",\"text\":{\"type\":\"plain_text\",\"text\":\"Github Status\"}},{\"type\":\"section\",\"text\":{\"type\":\"mrkdwn\",\"text\":\"$github_data\"}}]}`

10. Finally, make a curl request to the incoming webhook endpoint for posting an automated message
> curl -X POST -H "Content-type:application/json" --data "<formatted_message>" <webhook_endpoint>

## Execution
The script must be executed with the PAT(`Personal Access Token`) as the first argument
- `chmod +x ./jira_status_script.sh`
- `./jira_status_script.sh <PAT>`
  
*For public jira queries, you need no PAT
*This script can be scheduled as a cronjob on a local system or over a long running cluster*

