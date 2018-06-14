# Community-Monitoring

A Python framework to monitor Quali Community and report events to Strikedeck that occured in the prior day. <br /><br />


## Contents
1. [Running CloudShell Monitoring](#running)
2. [Reporting to Strikedeck](#reporting)
3. [Modules](#modules)
4. [Report New Event](#report)
<br />


<a name="running"></a>
## Running CloudShell Monitoring

Community Monitoring is expected to be run from the task scheduler. Create a task that will execute community_monitoring.bat at a certain time everyday. <br /><br />
It can also be run from the command line in the format, 'python community_monitoring.py'.
<br /><br />


<a name="reporting"></a>
## Reporting to Strikedeck

Community Monitoring is currently setup to report to Strikedeck. Each event will appear as it's own entry in Strikedeck. The event type will be one of the following strings matching the event. These are also the currently reported event types by community monitoring.

    Question Asked:      'Forum: New Question Asked'
    Article Posted:      'Forum: New Article Posted'
    Question Answered:   'Forum: New Answer Posted'
    Comment Posted:      'Forum: New Forum Comment Posted'
    Idea Suggested:      'Idea Box: New Idea Suggested'
    Idea Status Changed: 'Idea Box: Idea Status Changed'
    Idea Comment Posted: 'Idea Box: New Idea Comment Posted'
    Badge Given:         'Forum: Badge Given'

The event information to be passed along for each event type is covered on the *CloudShell Pro/VE Data Source Requirements: Community* within confluence. The API POST json format can be observed [here](https://cs.strikedeck.com/knowledge/event-api-details). The strings about will be the "event_type", which the additional information will be sent in the "data" dictionary. Currently, these events are also sent along with the Customer with "name" = "external_customer_id" = 'Quali Community Monitoring'.This is so that it shows up in the strikedeck sandbox environemnt. These events will be sent with the correct customer info when that information in compiled and available.<br /><br />


<a name="modules"></a>
## Modules

**Action**: Defines the Action Class

    Action.__init__ - create an action object, specifying the event type from EVENT_TYPE dict and json body of the event  
    Action.event_json - returns json of the Action, ready to be sent to Strikedeck

**community_info**: Module for getting info from Quali Community

    get_from_address - Simple GET from 'address', boolean paramters to return specifically the 'actions' or 'list' lists
    
**filter_info**: Module to filter info from Quali Community to create correct Action objects and only those from the previous day

    filter_users - Checks user json from Quali Community for Badges, get per user # of unanswered questions and new ideas
    filter_actions - Checks for the event type in json from Quali Community, creates Action objects for each
    
**strikedeck_reporting**: Module for reporting to Strikedeck

    create_json_report - creates a json for Strikedeck from one or more Action onjects given
    send_summary_to_strikedeck - POST json summary to strikedeck as /log or /logBulk
<br />

<a name="report"></a>
## Report New Event

The first step for adding a new event is to add to Action.py. In EVENT_TYPE, add a key:value entry with the key being an all caps identifier, and the value being the String that will be displayed as the event type in Strikedeck.<br /><br />

Next is to find how the specific event information can be queried from Quali Community. The AnswerHub API can be looked through [here](https://api.dzonesoftware.com/v2/reference). I recommend using Postman to test these API calls and search for the events you wish to report. There are two options currently implemeted.<br /><br />

**1. GET Action.json**:
The first option is how most of the current events are done. A GET request for the Action.json will return a list of actions. This list is iterated through and each action is checked for certain specifiers that mark it as a certain type of event. This process can be seen in filter_info.py, method filter_actions.<br /><br />

Using Postman, figure out the specifiers for this event type and create a conditional statement similar to the others in filter_info.py. Within this condition, create an Action object with the new EVENT_TYPE key and json body. The json body can be queried from Quali Community by getting the node specified by the node id. Then append this new Action onto the actions list that is returned from filter_actions.<br /><br />

**2. GET User.json**:
Some event info is not stored as simply. Badges given automatically are not included in the Action.json list and instead need to be searched for by iterating through every user in the system. You can observe how the badge given events are filtered in filter_info.py, filter_users.<br /><br />

The info found within a user's json, is used to make another GET request. For example, badges given needs 2 additional queries. First to /user/<user id>/award.json to get a list of that users awards. Then to /award/<award id>.json to get the award specific information. A similar process to this might be needed for certain event types. These events can be returned in a list and then extended onto the main actions list in community_monitoring.py, monitor_run.<br /><br />

**3. GET something else**:
There are many different event types and it is possible these 2 prior options will not cover them all. In that case, the AnserHub API and Postman are you best bets to figure it out.<br /><br />

**After the Action Objects are Created**:

    



