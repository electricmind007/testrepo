# User Usage Logging

Usage information of the CaaS platform and associated modules can be obtained by logging various events throughout a user's session. This information can then be reviewed manually, by targeted searching in Graylog, or by dashboards in Graylog or a tool like Grafana.

Most of the logging can be done through the existing caas-ui backend node.js application, however some events may need to have some functionality added so that specific actions that take place in the user interface can also be logged.

The events to capture are listed in the table below, along with whether they are "back-end" or "front-end" events. Note that the events denoted as "Front" end are client-side only actions, and will require some extra functionality in order to communicate with the server in order to log the event. These events are not critical to capture usage metrics and can be implemented after the initial release.

The events that need to be captured are listed in the following table:

| NAME | TYPE | DESCRIPTION | FRONT / BACK END |
| ---- | ---- | ----------- | ---------------- |
| QUESTION_ASKED | EVENT | The user asked a question and hit the Go button | Back
| USER_LOGIN | EVENT | The user logged in | Back
| LINK_CLICKED | EVENT | The user clicked a link in a response from a module | Front
| ISSUE_CREATED | EVENT | The user created a feedback issue | Back
| ISSUE_DIALOG_OPENED | EVENT | The user opened the feedback dialog | Front
| MODULE_RESPONSE | EVENT | A response was received from the analytic | Back
| MODULE_CONVERSATION | EVENT | The user provided "follow-up" information and hit Go | Back

The following information is also needed:

| NAME | TYPE | DESCRIPTION |
| ---- | ---- | ----------- |
| APPICATION | ENTITY | A name for the application for which the event is being logged |
| INTENT | ENTITY | The intent for the current scope |
| SESSION_ID | ENTITY | The id being used for the session |
| USER | ENTITY | The logged in user's id |
| TIMESTAMP | ENTITY | The time of the event |
| LINK | ENTITY | The link that was clicked by the user |

The messages should be logged with KEY=value pairs, and separated with a '|' (single pipe) character. This allows for future modification of information captured in the messages without affecting any tools that are extracting information as long as they are looking for KEY=value pairs. The order of the keys should be consistent as a general policy, but technically does not matter. A trailing '|' at the end of the message is optional. 

Note that not all possible KEYs will appear in every log message, only the relevant ones will be there. For example, LINK will be listed if the event was LINK_CLICKED.

Additional parameters and log information can also be provided. Such information could consist of the current caas-ui preamble (readable timestamp, platform), or any additional KEY=value pairs that may be relevant information but not listed in this document. The key information to capture are the events, and the users causing these events, so there shouldn't be much need for additional KEY=value parameters.

Some log message examples:

Thu, 11 May 2017 02:19:39 GMT platform:ui |EVENT=USER_LOGIN|USER=jdoe@ca.ibm.com|SESSION_ID=fdasjkl;fdsa|TIMESTAMP=12345678

Thu, 11 May 2017 02:19:39 GMT platform:ui |EVENT=LINK_CLICKED|USER=jdoe@ca.ibm.com|SESSION_ID=fdasjkl;fdsa|TIMESTAMP=12345679|INTENT=data-explorer:0.1:data-links|LINK=https://data-lake.zurich.ibm.com/explorer.html?industries=Retail&account+levels=DC&package=Contacts&company=Tim+Hortons

Thu, 11 May 2017 02:19:39 GMT platform:ui |EVENT=ISSUE_CREATED|USER=jdoe@ca.ibm.com|SESSION_ID=fdasjkl;fdsa|TIMESTAMP=12345679|P1=2559

Thu, 11 May 2017 02:19:39 GMT platform:ui |EVENT=MODULE_CONVERSATION|USER=jdoe@ca.ibm.com|SESSION_ID=fdasjkl;fdsa|TIMESTAMP=12345679|INTENT=data-explorer:0.1:data-links|FOLLOW_UP=for the retail industry|

### Implementation Notes
The logging functionality should be provided in a way that it can be used by other applications in a simple manner, such as by dropping in a logging.js file to the project. Each event and entity should be defined (i.e. constants or enum), and the application should be able to add additional events. The log messages should be written to stdout so that they can easily be captured by docker logs and then sent out to a centrally managed log server (i.e. made available to Graylog via fluentd / elasticsearch). Note that the scope of this functionality is limited to writing the log messages to stdout; centrally managed logs are maintained elsewhere.

