# NLQA Issue Maker
When a question submitted to the intent router does not find/return an answer the details need to be logged in a git-hub repository for review/action by either a subsequent manual/automated process. 

## Git-hub repository holds issues
New issues are created that contain specific information. These issues can be monitored to do incremental updates to different modules as feedback is collected. Data collected is specified in the issue_maker.thrift.

1. string question
1. list<IntentMatches> responses
  1. double confidence
  1. string id
  1. string version
  1. string owner
  1. set<string> accessibleToGroups
  1. string summary
  1. string description
1. double userRanking
1. string userComment

## IssueMaker Thrift Endpoint
A thrift endpoint that the UI can submit to. Additionally, the intent router can submit issues when no issues or low accuracy occurs.

1. Endpoint URL TBD

## UI Dialog
When a user is unsatisfied with the result, provide a dialog that can collect information to be submitted to the IssueMaker Thrift Endpoint. 

1. UI Dialog when no results found; triggered via `Feedback` button.
1. UI Dialog when user triggers `Feedback` button

