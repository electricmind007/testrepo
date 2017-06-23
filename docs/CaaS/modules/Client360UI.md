# Client 360 NLQA UI

## Intents

The UI design is client 360 intents specific. The intents are - 

- New buyer/client
- GOE
- Client's Net Promoter Score (NPS)
- Go to market strategy for clients
- Business opportunities with clients

### UI design for New Buyer

For new buyer usecase, the answer should have the total count of new buyers. The count is rendered through the directive `%md` which instructs the user interface to render the response from the NLQA module as Markdown.

The following directives will be supported - 

- `%table` (to render the list of new buyers as tables)

Each row within the table will contain the following information 
- the name of buyer
- email id
- country

For ambiguous questions (in absence of conversational service), the list will contain names of buyers grouped by countries. The results will be displayed as per the result set returned by Client 360 API endpoint. No further ordering, modifications, or cleaning will be done on the returned result set except for the fact that they will be grouped by countries (if not explicit from the question which country the user wants the results for). 


The following operations will be supported - 

- The list of new buyers will be available for download in CSV format. The user will be provided with a "save" button to save the list. Once user clicks on the save button, usual file save wizard will pop up. User will be free to choose the location where the list will be saved and the name of the file. 


- The table should display 10 buyers at a time. Users will be provided with options to click on prev/next arrows to move to previous 10 (or next 10) records respectively. The user will be able to see which page within the total number of pages of results they are viewing. 

## Assumption

As of now, client 360 questions will be directed to answers based on certain assumptions. Since CEDP 1.0 beta release will not have conversation/dialog module, the ambiguous questions by users will be directed to answers based on assumptions, and not by opening up dialogs of conversations with the user to clarify the ambiguities. For example, if a user asks about the number of net new buyers IBM has, the answers will be based on assumptions on a timeline. It will respond with only the number of new buyers since the assumed time or from a specific geographical location (eg. USA). 