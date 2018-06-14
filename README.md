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

The event information to be passed along for each event type is covered on the *CloudShell Pro/VE Data Source Requirements: Community* within confluence. The API POST json format can be observed [here](https://cs.strikedeck.com/knowledge/event-api-details). The strings about will be the "event_type", which the additional information will be sent in the "data" dictionary. Currently, these events are also sent along with the Customer with "name" = "external_customer_id" = 'Quali Community Monitoring'.This is so that it shows up in the strikedeck sandbox environemnt. These events will be sent with the correct customer info when that information in compiled and available.


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
    
**strikedeck_reporting**: Module 

    create_json_report - creates a json for Strikedeck from one or more Action onjects given
    send_summary_to_strikedeck - POST json summary to strikedeck as /log or /logBulk
    

<a name="report"></a>
## Report New Event

In order to add support for reporting a new event type, you must first look where




    
