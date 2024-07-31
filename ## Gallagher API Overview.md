## Gallagher API Overview

Command Centre REST API: Alarms and Events
This document describes how you can use the Command Centre REST API to download, monitor, and create events, and monitor and manage alarms.

It has companion documents describing the APIs for Cardholders, PIV cards, and non-cardholder items.

See the events section for examples of how to create events, download events, search for events using various criteria, and receive new events as they arrive.

See the alarms section for the methods you can use to download all current alarms, receive notifications as they change state, and manage them. These methods offer similar alarm-management functions to those in the Premier and Command Centre clients.

Helper methods allow you to look up Command Centre's internal identifiers for cardholders, divisions, and other items, so that you can use those identifiers in search filters.

Licensing
All of the GETs in this document and the POSTs that manage alarms require the RESTEvents licence. The POST to create a new event requires RESTCreateEvents.

The server will return a 403 if you attempt an operation for which the server is not licensed.

Versions
The body of this document clearly indicates when recent features arrived in the API so that readers with older versions of Command Centre know not to expect them.

Alarms, events, and items API changes on the roadmap
You will be able to create and delete divisions, and modify some basic fields.
Alarms and events API changes in 9.20
You can filter by events' direct division. The current division search is recursive: it returns all events that have any of the filter's divisions anywhere in their ancestry. The new division search is shallow: it only returns events that are direct members of the filter's divisions.
Alarms and events API changes in 9.10
You can request the name of an alarm's instruction, and the contents of the instruction itself.
Alarms and events API changes in 9.00
A new event type hasLocation greatly simplifies the monitoring of cardholder movements, and

a new location block on events greatly simplifies interpreting them ( introduction, use case, schema ).

Alarms and events API changes in 8.90
BREAKING CHANGE: the operator must have the 'Create Events and Alarms' privilege in the division of the source item, if your request specifies a source item. Current versions only require that the operator has that privilege on at least one division.

To improve the symmetry between events and alarms, they both now carry a field eventType containing the ID and name of their type. The type field, which has different contents for alarms and events, is now obsolete (but still returned in results).

Alarms contain a new field called event containing a link to the corresponding event, mirroring the existing alarm field coming the other way from an event.

You can request a division's description. This has been possible since 8.50 but was not certified until 2022, so it gets a mention here.

Alarms and events API changes in 8.70
Each event in a page of search results will contain URLs to continue the search after that event. This is a significant benefit to integrations that extract large pages of events and may encounter a problem mid-page, and have to resume without loss or duplication later.

A new privilege 'View Cardholder Events' grants visibility of an event if the operator has the privilege in both the event's division and the event's cardholder's division. This allows a site to create a REST client that can monitor selected cardholders' movements through selected areas, but cannot see any other activity in those areas.

You may now use the relatedItem query parameter to find events that are associated with any item.

Alarms and events API changes in 8.60
Card numbers on Bluetooth (mobile) and PIV card events are now full-length. Mobile numbers are now the phone's ID string, changed from the number that 8.50 returned. Decimal numbers now come out unsigned. Note that neither 8.50's numbers nor 8.60's ID strings are guaranteed unique across phones.

Access events that do not have a card, such as someone entering their user code instead of badging their card, will no longer return a card block.

Alarms and events API changes in 8.50
The server property that turns off client certificate checking changed.

Divisions can return some of their Visitor Management configuration.

You can view any item's notes. You have been able to for a while now, but it was missing from this document.

You can request a division's description.

Alarms and events API changes in 8.40
You may now use the fields query parameter to tailor the fields that come back for events and alarms.

Operator add, modify, and delete events now contain a link to the affected item in a new modifiedItem block, deprecating the accessGroup block for those event types.

All operator events now contain the operator's name at the time.

All events now contain the name of the event's division.

The item search can now filter multiple item types.

Events now show their origin when they have arrived from a remote server (in a multiserver environment).

Alarms and events API changes in 8.30
The items API has methods that let you monitor the status of large numbers of items.

Doors related to guard tour events now appear in an event's door block.

Alarms and events API changes in 8.20
Alarms and events with a related cardholder now show the cardholder's current first and last name in separate fields.

The name field on an alarm or event with a related cardholder is now the cardholder's name at the time of the event, rather than at the time of the request.

You can use the 'fields' parameter to add the 'details' field to an event summary.

Alarms and events API changes in 8.10
Incoming events
Events now show their related access groups and doors for external event types.
Request Content-Types: application/json
Response Content-Types: application/json
Schemes: https
Version: 1.0.0
Authentication
API key 
Clients authenticate by including a pre-shared API key in the Authorization header of each request. Command Centre generates an API key when you create an endpoint for your clients to connect to. Search the Configuration Client online help for 'REST API' for how do do that.

In current versions of Command Centre, the API key will be in the format XXXX-XXXX-XXXX-XXXX-XXXX-XXXX-XXXX-XXXX with each X an uppercase hexadecimal digit. Future versions of Command Centre may use other formats of API key, so treat it as an opaque string. Tempting as it may be, do not interpret is as a GUID.

There are two ways you can pass the API key to the server: following an authorisation method of GGL-API-KEY, or (in 9.0 or later) in the style of HTTP Basic authentication: prefixed with a colon, Base64-encoded, and following an authorisation method of Basic.

Examples:

Authorization: GGL-API-KEY C774-B01F-D695-AA4B-215F-A158-AC22-ADEB

Authorization: Basic OkM3NzQtQjAxRi1ENjk1LUFBNEItMjE1Ri1BMTU4LUFDMjItQURFQg==

Early versions of Command Centre allowed you to omit the 'GGL-API-KEY' from the first form. Later versions of CC and all versions of the API gateway will reject you with a 401 if you do that. The API gateway is especially fussy about case and punctuation.

HTTP Basic authentication
The two example headers above are equivalent: prepending a colon to the API key beginning with C774 then encoding it into Base64 produces the 56 characters beginning with OkM3.

According to the HTTP Basic specification, the colon is a separator between a username and a password. In our case, the API key is the password and the username is unnecessary. If you place a username before the colon, before Base64-encoding it, Command Centre will ignore it and authenticate the connection based on the API key alone.

Pinning a client TLS certificate
Depending on Command Centre's site configuration, its REST API may also require a client certificate with each request. In versions up to and including 8.40 this was controlled by a flag labelled 'Do not require pinned client certficates' on the 'Web Services' tab of the server properties. In 8.50 that flag's label changed to 'Enable REST Clients with no client certificate', and its behaviour changed slightly when turned on.

If off, for all versions of CC, an incoming request's certificate has to match the thumbprint on its API key's REST Client item, and REST Client items with a blank thumbprint field do not work at all. This is how it ships, and it is the recommended way of running a production server.

If 'Do not require pinned client certificates' was turned on in versions up to and including 8.40, the server did not check any client certificates. If that flag is turned on in 8.50, now relabelled 'Enable REST Clients with no client certificate', it still does not check the client certificate if the thumbprint field of the matching REST Client item is blank, but if the Client item has a thumbprint, the server will reject connections with the wrong certificate.

See the Configuration Client help for instructions on where to enter REST Client thumbprints.

Source IP filtering
Also note that if IP filtering is enabled on the REST Client item in Command Centre, the API will only accept connections from the IP address ranges configurated into that client item.

Symptoms of authentication failures
If a connection attempt fails, the server will return a 401 and raise an event containing its reason for refusing the request. If that happens too often, the server will stop reporting each offence and will instead create a summary alarm at a much lower rate. The details of the alarm tell you how many failed attempts there have been since the start of the flood. The server will stay in this mode of reduced reporting until several minutes pass without a failed connection attempt.

The current failure limit is ten errors inside one minute. After that you will receive one "a large volume of requests has been denied" alarm every minute while the failures continue until five minutes passes without a failure.

The most common queries we receive from our integrators relate to their certificate handling. If your client's HTTP client library complains about certificates the first thing to check is Command Centre's alarm list. If there are 'invalid client certificate' alarms there, your client is not sending the certificate CC is expecting. If there are no alarms then the client is most likely rejecting the server's certificate.

type
apiKey
name
Authorization
in
header
Documentation suite
 

Best viewed in Chrome or Firefox. Microsoft Edge does not render the table of contents correctly in the orange column on the left, making navigation difficult.

The API's reference documentation divides into:

Alarms, events, non-cardholder items, and bulk status monitoring	
The alarms and events APIs let you download, monitor, and create events, and download, monitor, and manage alarms.

The alarms and events documentation also covers API calls that support divisions and items as they relate to events, as well as bulk status monitoring.

Cardholders and related items	The cardholder parts of the API let you manage your users, their personal data and credentials (cards), and their links to associated items such as access groups, roles, operator groups, competencies, and lockers.
Status and overrides	
These functions let you monitor and override the types of Command Centre items that have their own status, including access zones, alarm zones, doors, fence zones, inputs, outputs, macros, elevator groups, interlock groups, and schedules.

Despite its name this section does not cover mass-monitoring item status.

PIV cards	This supplement describes how to work with PIV and PIV-I cards. It is separate from the main cardholder documentation in the interest of brevity.
Forward compatibility (HATEOAS)
This is a self-referencing REST API that follows the principles of HATEOAS. Other than the initial GET to /api when it first connects, your source code should not contain any URLs, as they are subject to change. You should append the query parameters this document describes for operations such as filtering and searching, but everying in the path should come from the results of /api or pages linked from it.

/api only shows licensed API calls.

Be prepared to append query parameters to URLs that already have their own: do not assume that you can simply add a question mark and your parameters.

Operator privileges
First, some background on operators, operator groups, and divisions.

To determine your REST client's privileges, the server starts by searching the list of REST Client items in its configuration for the one with the API key in the Authorization header of your request. Assuming it finds one, it takes the cardholder from that REST Client item.

To be any use at all that cardholder must be a member of at least one operator group. Being in an operator group makes a cardholder an operator.

After the server has your operator, it needs to check whether that operator has access to the items or events in the request. To explain that, it is necessary to cover divisions.

Aside from some special items such as day categories, every item in Command Centre is in a division. Divisions are hierarchical, with all divisions (but the root) being a child of another. Multi-server installations have one division tree per server.

Every event and alarm has a division. It is usually the division of the source item. Card events or 'forced door' alarms, for example, typically use a door as a source item; when an operator modifies an item, the event recording that change uses that operator's workstation as the source item; the alarm that the server generates when you send a bad API key uses the server item as the source.

An event's division is not always its source's division. 'Card activated' events have the server as a source item, but take on the cardholder's division so that operators who can see the cardholder can also see when the server activates their cards.

To link privileges to items and events, an operator group contains a list of privileges and a list of divisions. Its operators enjoy those privileges on all the items, events, and alarms that are in those divisions.

Having a privilege on a division also grants an operator that privilege on that division's descendants. Therefore an operator with a privilege on the root division has that privilege on all that server's items and events.

A common misconception is that the division an operator is in, or the division an operator group is in, have a bearing on the operator's privileges. They do not. It is all about the divisions in the operator group's 'Operator privileges' list.

If your privileges do not seem to be working
First, to protect Command Centre from accident and malice, you should strive to grant your REST clients the fewest and lowest-level privileges you can. Do not give them 'advanced user'. Reaching a point where you do not receive the results you want is a good thing: it means you have screwed things down a little too tightly.

If changing privileges in Command Centre does not seem to make any difference, remember to push the 'Refresh operator privileges' button in the properties window of your REST Client item.

Here are some general rules that may help if you are still not receiving the results you expect.

Receiving a 403 from a GET means the server may not have a license for the retrieval you are attempting or your operator does not have the privilege to view that object. If it is a licensing problem, the body of the response will describe it.

Receiving a 403 from a PATCH, POST, or DELETE means your operator does not have the necessary privilege to perform that operation. You need one of the privileges beginning with 'Edit', 'Modify', or 'Create'.

Receiving a 404 when trying to get one item or event means it either does not exist or your operator does not have the privilege to view it.

Receiving a 200 and an empty result set from a search means you are licensed for that search but your operator could not see any items or events that matched. One possible cause is that your operator is not licensed to see any items or events of that kind. Make sure that one of the operator groups that your operator is in has a 'View' privilege such as 'View events' or 'View cardholders' - whatever is appropriate. If it does, and you have hit the button mentioned above, then check that the divisions on the operator group contain the items you expect. If it is events you are searching for, check the source item on those events either by using a REST operator with 'View events' on the root division or by looking at them in one of the thick clients.

Text encoding
Command Centre's REST API encodes all payloads using UTF-8, and expects clients to do the same. It does not escape special characters in response bodies except where required to embed them in JSON.

Specifically, it does not sanitise HTML, XML, or SQL. Your clients should expect to receive strings exactly as they were sent, even if they were sent by a hostile client. Write your clients to resist injection attacks.

Field specifiers (in general)
You can specify the fields you want in the results of a query. This lets you:

save calls by adding more fields to search results, which are terse by default
save bandwidth by putting fewer fields on detail pages, which are verbose by default, and
retrieve more data by adding fields to detail pages that are not normally there, such as an item's status, short name, and notes.
You do this using the query parameter fields to the search, detail, and updates pages. It takes a comma-separated list of field names. They are case-sensitive: copy them carefully from this document. The special field name defaults adds in the fields the server would have sent had you not set the fields parameter at all.

Example GETs:

/api/cardholders?name=Smith&fields=defaults,cards,competencies will list all cardholders with 'Smith' in the first or last name, showing the fields you normally get on a search page plus their cards and competencies.

/api/access_groups/123?fields=children,cardholders will show just the child groups of access group 123 and a link to retrieve its cardholders - nothing else.

/api/outputs?fields=name,shortname,id will show the name, short name, and ID of your outputs.

/api/access_zones?fields=name,doors will show the name and attached doors of your access zones.

/api/alarms?fields=time,message,details will return the time, message text, and details text of your alarms. Not only does that save a lot of data by removing default fields, but it adds the details field which does not normally come out of an alarm search.

Each controller has different optional fields - see their sections under Operations.

Event field specifiers
What you can do with the fields query parameter on an events method has improved through the versions, so explaining it is a topic in itself.

In 8.40 and later the values you can list are the same as the field names in the details page, plus the special value for a personal data field described below. You can pick whichever fields you want, including defaults, though we urge you to only list the fields you need. Anything you wrote for 8.30 or earlier will work in 8.40 as it did before.

In versions up to 8.30 you can only add fields to the event summary and details pages, not remove them. If you send the parameter it must start with fields=defaults. That gives you the default set. What you can add after that depends on the version of Command Centre you're calling.

In 8.10 and earlier the only field you can add is cardholder.pdf_XXXX, where XXXX is the ID of a PDF. Find that ID with a query to the PDFs controller. Don't forget a separating comma between it and defaults.

That will add a cardholder's PDF to the events that are related to them.

You can only pick one PDF. It will only appear on events that have a related cardholder, such as access events, because without a cardholder there is no PDF. The security model applies too, so it will only appear if your REST operator has the appropriate privileges on that cardholder and PDF.

In 8.20 and 8.30 you can also add details. Make sure you have a separating comma after defaults or cardholder.pdf_XXXX.

An event detail string can be kilobytes long so we left it optional. It is the only field in the details page that is not in the search results, by the way, so if you are running 8.20 or later you can add it to your search results and you will not need the details page.

Server version	Valid fields parameter values
Older than 8.20	defaults,cardholder.pdf_XXXX
8.20 to 8.30	defaults,cardholder.pdf_XXXX
or
defaults,details
or
defaults,cardholder.pdf_XXXX,details
8.40 and later	any
Alarms
Use these methods to download, monitor, and manage Command Centre alarms.

Alarm use cases
Downloading and managing unprocessed alarms
GET /api
Follow the link at features.alarms.alarms ↪. You will receive up to 100 alarms, each containing links to its management functions.
If step 2 returned results and there is a link at next.href, follow it and repeat.
Staying up to date
After getting all the current alarms using the process above, follow the link at updates.href. It will block until there is a new alarm or a change to an existing alarm.

Alarms
Get current alarms
GET /api/alarms
This returns the current list of unprocessed alarms. The result will contain no more than 100 alarms; you should follow the next link, if it is present, to collect more.

You can tell when you have loaded all the current alarms because there will not be a next link. Instead, there will be an updates link, which you may then follow to long-poll for live updates to alarms.

The alarm summary only contains unprocessed alarms, but you can still access processed alarms by finding them in the event summary and following its alarm.href link to the alarm details.

Unlike the corresponding method that retrieves events, this call does not take query parameters to filter its results. You can limit the fields it returns, but it will always return every alarm that your operator can view. If you wish to restrict it to alarms in certain divisions, give your operator permission to view alarms in only those divisions.

Do not code this URL into your application. Take it from alarms.alarms.href in the results of GET /api.

fields
in query
string  href, id, time, message, source, type, eventType, priority, state, active, division, notePresets, view, comment, acknowledgeWithComment, acknowledge, processWithComment, process, details, history, instruction, cardholder, event 
Sets the fields you want in your results. Separate fields with commas.

New to 8.40.

200 OK
Alarm search
Success

403 Forbidden
The site does not have a RESTEvents licence.

Response Example (200 OK)
{
  "alarms": [
    {
      "href": "https://localhost:8904/api/alarms/10135",
      "id": "10135",
      "time": "2016-02-18T19:21:52Z",
      "message": "External bulk loading bay door has been forced",
      "source": {
        "id": "1321",
        "name": "External bulk loading bay door",
        "href": "https://localhost:8904/api/doors/1321"
      },
      "type": "Forced door",
      "eventType": {
        "id": "23035",
        "name": "Forced door"
      },
      "priority": 8,
      "state": "unacknowledged",
      "active": false,
      "division": {
        "href": "https://localhost:8904/api/divisions/2"
      },
      "event": {
        "href": "https://localhost:8904/api/events/10135"
      },
      "notePresets": [
        "False alarm confirmed by surveillance",
        "Security staff dispatched"
      ],
      "view": {
        "href": "https://localhost:8904/api/alarms/92210/view"
      },
      "comment": {
        "href": "https://localhost:8904/api/alarms/92210/comment"
      },
      "acknowledge": {
        "href": "https://localhost:8904/api/alarms/92210/acknowledge"
      },
      "acknowledgeWithComment": {
        "href": "https://localhost:8904/api/alarms/92210/acknowledge"
      },
      "process": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      },
      "processWithComment": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      },
      "forceProcess": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      }
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/alarms/next?start=92143&pos=61320"
  },
  "updates": {
    "href": "https://localhost:8904/api/alarms/updates?id=92143.1"
  }
}
Alarms
Get changes to alarms (or wait)
GET /api/alarms/updates
Long poll this link for live updates to alarms. If alarms occurred or changed since the previous call, no matter how long ago that was, it will return them immediately. If there are no updates pending, it will wait until one arrives. If none arrive before a timeout passes (about 30 seconds) the updates array in its response will be empty.

Whether or not the response contained updates the client should follow the next link to wait for the next updates. If your server receives a lot of alarm updates, wait some time between calls to reduce its load. You will not miss any updates: each response contains everything that happened since the previous call.

Do not wait if you receive 100 updates or more. 100 is the maximum number of updates current versions of CC will return in one batch, so if that happens you have fallen behind: there are more updates waiting and you can catch up by asking for them immediately.

Command Centre does not tell you which alarms were added, removed, or modified. It is up to you to match the incoming alarms against your own internal alarm list and determine the differences.

Do not code this URL into your application. Take it from alarms.updates.href in the results of GET /api, or from updates in the results of GET /api/alarms, or from next in the results of this call. Do not attempt to interpret or set the id query parameter in the URL, as it tracks your progress through the alarm history.

Assuming you got the URL for this call from the response from another call, and your operator therefore has the privileges required to view alarms, this should always complete succesfully.

fields
in query
string  href, id, time, message, source, type, eventType, priority, state, active, division, notePresets, view, comment, acknowledgeWithComment, acknowledge, processWithComment, process, details, history, instruction, cardholder, event 
Sets the fields you want in your results. Separate fields with commas.

New to 8.40.

200 OK
Alarm updates
Success

403 Forbidden
The site does not have a RESTEvents licence.

Response Example (200 OK)
{
  "updates": [
    {
      "href": "https://localhost:8904/api/alarms/10135",
      "id": "10135",
      "time": "2016-02-18T19:21:52Z",
      "message": "External bulk loading bay door has been forced",
      "source": {
        "id": "1321",
        "name": "External bulk loading bay door",
        "href": "https://localhost:8904/api/doors/1321"
      },
      "type": "Forced door",
      "eventType": {
        "id": "23035",
        "name": "Forced door"
      },
      "priority": 8,
      "state": "unacknowledged",
      "active": false,
      "division": {
        "href": "https://localhost:8904/api/divisions/2"
      },
      "event": {
        "href": "https://localhost:8904/api/events/10135"
      },
      "notePresets": [
        "False alarm confirmed by surveillance",
        "Security staff dispatched"
      ],
      "view": {
        "href": "https://localhost:8904/api/alarms/92210/view"
      },
      "comment": {
        "href": "https://localhost:8904/api/alarms/92210/comment"
      },
      "acknowledge": {
        "href": "https://localhost:8904/api/alarms/92210/acknowledge"
      },
      "acknowledgeWithComment": {
        "href": "https://localhost:8904/api/alarms/92210/acknowledge"
      },
      "process": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      },
      "processWithComment": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      },
      "forceProcess": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      }
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/alarms/updates?id=10135"
  }
}
Alarms
Get details of an alarm
GET /api/alarms/{id}
Full details for an alarm. Follow the href in the alarm summary to get here.

id
in path
string
The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

fields
in query
string  href, id, time, message, source, type, eventType, priority, state, active, division, notePresets, view, comment, acknowledgeWithComment, acknowledge, processWithComment, process, details, history, instruction, cardholder, event 
Sets the fields you want in your results. Separate fields with commas.

New to 8.40.

200 OK
Alarm detail
Success

404 Not Found
The alarm ID is invalid or you do not have privileges for the alarm.

Response Example (200 OK)
{
  "href": "https://localhost:8904/api/alarms/10135",
  "id": "10135",
  "time": "2016-02-18T19:21:52Z",
  "message": "External bulk loading bay door has been forced",
  "source": {
    "id": "1321",
    "name": "External bulk loading bay door",
    "href": "https://localhost:8904/api/doors/1321"
  },
  "type": "Forced door",
  "eventType": {
    "id": "23035",
    "name": "Forced door"
  },
  "priority": 8,
  "state": "unacknowledged",
  "active": false,
  "division": {
    "href": "https://localhost:8904/api/divisions/2"
  },
  "event": {
    "href": "https://localhost:8904/api/events/10135"
  },
  "notePresets": [
    "False alarm confirmed by surveillance",
    "Security staff dispatched"
  ],
  "view": {
    "href": "https://localhost:8904/api/alarms/92210/view"
  },
  "comment": {
    "href": "https://localhost:8904/api/alarms/92210/comment"
  },
  "acknowledge": {
    "href": "https://localhost:8904/api/alarms/92210/acknowledge"
  },
  "acknowledgeWithComment": {
    "href": "https://localhost:8904/api/alarms/92210/acknowledge"
  },
  "process": {
    "href": "https://localhost:8904/api/alarms/92210/process"
  },
  "processWithComment": {
    "href": "https://localhost:8904/api/alarms/92210/process"
  },
  "forceProcess": {
    "href": "https://localhost:8904/api/alarms/92210/process"
  },
  "details": "Forced door",
  "history": [
    {
      "time": "2016-02-18T19:21:52Z",
      "action": "viewed",
      "comment": "Operator viewed alarm properties",
      "operator": {
        "name": "System Operator"
      }
    }
  ],
  "instruction": {
    "href": "https://localhost:8904/api/alarms/92210/instructions",
    "name": "Forced door instruction"
  },
  "cardholder": {
    "href": "https://localhost:8904/api/cardholders/325",
    "name": "Smith, Jane",
    "firstName": "Jane",
    "lastName": "Smith-Jones"
  }
}
Alarms
Mark an alarm as viewed
POST /api/alarms/{id}/view
Mark the alarm as viewed. Follow the view link in the alarm summary to get here.

You can do this for an alarm in any state, even processed, provided you have its URL and sufficient privilege.

Alarm update request
Optional comment.

id
in path
string
The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

Request Example
{
  "comment": "Alarm was adequately explained."
}
200 OK
Success

404 Not Found
Alarm ID is invalid or you do not have privileges for the alarm.

Alarms
Add a comment to an alarm
POST /api/alarms/{id}/comment
Follow the comment link in the alarm summary to get here.

You can comment on an alarm in any state, even processed, provided you have its URL and sufficient privilege.

Alarm update request
The comment string.

id
in path
string
The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

Request Example
{
  "comment": "Alarm was adequately explained."
}
200 OK
Success

404 Not Found
Alarm ID is invalid or you do not have privileges for the alarm.

Alarms
Mark an alarm as acknowledged
POST /api/alarms/{id}/acknowledge
Follow the acknowledgeWithComment or the acknowledge link in the alarm summary to get here.

You can acknowledge an alarm that is in any state provided you have its URL and sufficient privilege. Doing so will add an entry to the alarm's history, but will have no other effect if it is already acknowledged or processed.

Alarm update request
Optional comment.

id
in path
string
The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

Request Example
{
  "comment": "Alarm was adequately explained."
}
200 OK
Success

404 Not Found
Alarm ID is invalid or you do not have privileges for the alarm.

Alarms
Mark an alarm as processed
POST /api/alarms/{id}/process
Follow the processWithComment, forceProcess, or process link in the alarm to get here.

You can process an alarm that is in any state provided you have its URL and sufficient privilege. Doing so will add an entry to the alarm's history, but will have no other effect if it is already acknowledged or processed. If the alarm is active -- waiting for another event to restore it (such as a 'door open too long' waiting for the door to close) -- it will perform a force process.

Alarm update request
Optional comment.

id
in path
string
The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

Request Example
{
  "comment": "Alarm was adequately explained."
}
200 OK
Success

404 Not Found
Alarm ID is invalid or you do not have privileges for the alarm.

Divisions
These methods provide access to the Command Centre divisions that are available to the REST client. Call /api and use the link at features.{your_feature}.divisions.href to retrieve the divisions in which the REST operator has privileges for that feature. Pick out the ones of interest and use those IDs in event, alarm, or item searches if you don't want the search to scan everything.

Or, you may end up here by following a division's link from a reception item when you need to retrieve the visitor management configuration for that reception's division.

Licensing
Every REST licence enables the divisions controller: RESTEvents, RESTCreateEvents, RESTCardholders, RESTStatus, and RESTOverrides.

Divisions
Create a division [coming soon]
POST /api/divisions
Creates a new division.

The POST expects a document in the same format as the the division detail but with far fewer fields. An example is this POST example. The only mandatory field is parent.

When successful it returns a location header containing the address of the new division.

Note that you can only create one division per POST.

Do not code this URL into your application. Take it from the results of GET /api.

/api only shows API features for which you have the necessary licence.

Creating a division requires the RESTCardholders licence.

It is coming to a future version of Command Centre.

Division PATCH and POST example
The only required field here is the new division's parent. All divisions (except the root) must have a parent.

Request Example
{
  "name": "Long division",
  "description": "Quatermasters",
  "notes": "A very long string.",
  "parent": {
    "href": "https://localhost:8904/api/divisions/2"
  }
}
201 Created
Success. Check the response body for feedback about your request.

400 Bad Request
The body of the POST did not describe a valid division. This includes not specifying a parent division. Or it may be that you tried to give the new division the same name as an existing one. Or you may have tried to set the new division's parent to a division that is not visible to you.

See the body of the response for help.

403 Forbidden
The operator does not have a privilege on the parent division that allows creating divisions inside it ('Configure Site' or 'Edit Divisions'), or the server has reached its licensed limit of divisions.

Response Headers (201 Created)
location	
The href of the new division.

string (url)
Divisions
List divisions
GET /api/divisions/{operation}
The functions inside /api/divisions/ retrieve the divisions in which the operator can perform other functions. They all return the same data structure.

For example, /api/divisions/view_events retrieves the list of divisions in which the REST operator has privileges to view events, and /api/division/view_alarms does the same for alarms.

Do not code these URLs into your application. Take them from the results of GET /api. For the events and alarms examples, the links will be at events.divisions.href and alarms.division.href.

/api only shows API features for which you have the necessary licence.

top
in query
integer x ≥ 1
Sets the maximum number of divisions to return per page.

sort
in query
string  id, name, -id, -name 
Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. This is known as "page drift." Sorting by ID does not carry that risk.
Following a next link is dramatically quicker when sorting by ID.
We strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

The server silently ignores anything except the options listed here.

fields
in query
string  href, id, name, parent, visitorManagement 
Return these fields instead of the default set. The values you can list are the same as the field names you would see in the results, plus visitorManagement, which does not come out by default. Use it to specify the fields you want in your results. Separate values with commas.

Treat the string matches as case-sensitive.

200 OK
Divisions
Success

403 Forbidden
The site does not have a REST licence.

Response Example (200 OK)
{
  "results": [
    {
      "href": "https://localhost:8904/divisions/2",
      "id": "2",
      "name": "Root division",
      "description": "Contains all other divisions",
      "serverDisplayName": "ruatoria.satellite.int",
      "parent": {
        "href": "https://localhost:8904/divisions/2"
      },
      "visitorManagement": {
        "active": true,
        "visitortypes": [
          {
            "href": "https://localhost:8904/api/divisions/2/visitor_types/925",
            "accessGroup": {
              "name": "Visitor access group 1",
              "href": "https://localhost:8904/api/access_groups/925"
            },
            "hostAccessGroups": [
              {
                "accessGroup": {
                  "name": "Host access group 1",
                  "href": "https://localhost:8904/api/access_groups/938"
                }
              }
            ],
            "visitorAccessGroups": [
              {
                "accessGroup": {
                  "name": "Access group 22",
                  "href": "https://localhost:8904/api/access_groups/926"
                }
              },
              {
                "accessGroup": {
                  "name": "Access group 30",
                  "href": "https://localhost:8904/api/access_groups/927"
                }
              }
            ]
          }
        ]
      }
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/divisions/view_events?skip=10"
  }
}
Divisions
Get details of a division
GET /api/divisions/{id}
Details of a division. Follow the href in a division summary to get here.

id
in path
string
The ID of the division.

fields
in query
string  href, id, name, parent, visitorManagement 
Return these fields instead of the default set. The values you can list are the same as the field names you would see in the results, plus visitorManagement, which does not come out by default. Use it to specify the fields you want in your results. Separate values with commas.

Treat the string matches as case-sensitive.

200 OK
Division
Success

403 Forbidden
The site does not have a REST licence.

404 Not Found
That is not the href of a division or you do not have privileges to read divisions (View Site, Configure Site, Edit Divisions, or Advanced User).

Response Example (200 OK)
{
  "href": "https://localhost:8904/divisions/2",
  "id": "2",
  "name": "Root division",
  "description": "Contains all other divisions",
  "serverDisplayName": "ruatoria.satellite.int",
  "parent": {
    "href": "https://localhost:8904/divisions/2"
  },
  "visitorManagement": {
    "active": true,
    "visitortypes": [
      {
        "href": "https://localhost:8904/api/divisions/2/visitor_types/925",
        "accessGroup": {
          "name": "Visitor access group 1",
          "href": "https://localhost:8904/api/access_groups/925"
        },
        "hostAccessGroups": [
          {
            "accessGroup": {
              "name": "Host access group 1",
              "href": "https://localhost:8904/api/access_groups/938"
            }
          }
        ],
        "visitorAccessGroups": [
          {
            "accessGroup": {
              "name": "Access group 22",
              "href": "https://localhost:8904/api/access_groups/926"
            }
          },
          {
            "accessGroup": {
              "name": "Access group 30",
              "href": "https://localhost:8904/api/access_groups/927"
            }
          }
        ]
      }
    ]
  }
}
Divisions
Update a division [coming soon]
PATCH /api/divisions/{id}
This is the call you use to update a division's name, description, notes, or parent.

The PATCH expects a document in the same format as the the division detail but with fewer valid fields. An example is this PATCH example.

Patching divisions is coming to a future version of Command Centre.

Division PATCH and POST example
There are no mandatory fields.

Request Example
{
  "name": "Long division",
  "description": "Quatermasters",
  "notes": "A very long string.",
  "parent": {
    "href": "https://localhost:8904/api/divisions/2"
  }
}
200 OK
Success. The response body will contain feedback from the server about your PATCH.

204 No Content
Success.

400 Bad Request
The body of the PATCH did not describe a valid division. See the body of the response for help on what went wrong. It may be that you tried to use the name of another division: no two items of the same type can have the same name. Or you may have tried to set the division's parent to a division that is not visible to you.

403 Forbidden
The operator has a privilege that allows viewing the division but not modifying it, or you tried to set a parent on the root division, or you tried to set the parent to a division you cannot configure, or the server is missing the necessary licence.

You need either the 'Configure Site' or 'Edit Divisions' privilege on the division you are changing. You also need it on the new parent, if you are changing that.

404 Not Found
That is not the URL of a division or the operator does not have the privilege to view that division. This probably means you have built the URL yourself instead of taking it from the results of a GET.

409 Conflict
The item is locked for editing by another operator. The body of the response will tell you which operator is holding the lock.

Divisions
Remove a division
DELETE /api/divisions/{id}
This call removes a division from Command Centre.

Deleting divisions will be possible in a future version of Command Centre.

204 No Content
Success.

400 Bad Request
Deleting the division failed. This happens when the division is not empty or is still being used by another item (such as an operator group).

403 Forbidden
The operator has the permission to view the division but not delete it, or the server is not licensed for the operation.

404 Not Found
That is not the URL of a division, or the operator is not privileged to view it.

Events
Use the GET methods in this API for historical searches or to stay up to date with new events as they occur. Use the POST method (added in version 8.10) to create events of your own.

There are no PATCH actions on events, because they are immutable. If an event is also an alarm it carries some changeable state and a log of activity and comments, but the underlying event never changes.

The API only returns events that are still in Command Centre's database. It will not return events that have been removed, even if they are in an archive file.

Location events in 9.00
Command Centre has about 80 event types that occur when somebody authenticates at a device, usually by badging a card. Some of those event types also contain the access zones that the person started and finished in. Version 9.00 introduced some improvements that presented the cardholder and the two zones to API clients in a consistent way:

A new event type hasLocation that lets you filter for only those events that reveal a cardholder's location. If your server is 9.00 or better, use it in the type query parameter as you would normally use an integer event type: type=hasLocation. Gallagher will update this as new versions add more event types. Do not use it on 8.90 or earlier: those servers will reject it.

A new location block on events of those types that contains fields containing the cardholder and their location.

For more on using these new features, see the relevant use case below (search for 'location').

Event use cases
Downloading the entire event database
GET /api
Follow the link at features.events.events.href ↪. You can add query parameters to alter the search. If you are running 8.70 or later and there is a danger that you might encounter a problem while processing a batch of events you should also use fields to get the next link for each event. Note that this is different from the next link you receive at the end of each result set.
Process the events you receive in that call. If you asked for the next field, write the link for each event you successfully process to disk before attempting the next one. Then, if the worst happens, your client can read that link from disk and pick up where it left off.
If there were results, follow the link at next.href and repeat.
If you then wish to stay up to date, switch to following the link at updates.href ↪ in the results. That call will block until more events arrive, or a minute passes (approximately).

Downloading the most recent events
Get /api
Take the link at features.events.events.href ↪ and append previous=true&top=20. Add the appropriate query separator ? or & first, depending on whether there is a query parameter in the URL already, and change the 20 as appropriate. The default is 1000, which is probably more than you want.
Process the events you receive in that call.
Follow the link at previous.href to get earlier events, or next.href or updates.href for later events. The last is a long poll, which means that if no events are ready when you make the call it will block until new events arrive.
Reporting on events over a period.
GET /api
Follow the link at features.events.events.href ↪ appending after=2017-01-01Z&before=2017-02-01Z or whatever timestamps are appropriate (after the correct query separator, ? or &).
Remember that some remote systems take their time sending events to Command Centre, so do not be too hasty running your reports. If you fire them off at the stroke of midnight, you may miss events that occurred before midnight but have not yet arrived at Command Centre.

Receiving new events as they occur, starting from now
GET /api
Follow the link at features.events.updates.href ↪ adding query parameters containing your search terms. The call will block until at least one matching event arrives at the server.
Process the events you receive in that call. Often there is only one: the first that arrived after you made the call.
Sleep to reduce load on the server.
Loop, following the link at updates.href after processing each batch of events. It will return immediately if there are new events waiting, or it will block until new events arrive. Follow the advice in downloading the entire event database about keeping track of your position if you might encounter a problem writing events mid-batch. Note that that advises using the next field, but you will be using the updates field, which is the same thing in long-poll form.
Receiving new events as they occur, starting from now, with no long polls
GET /api
Follow the link at features.events.events.href ↪ and append previous=true&top=1. Add the appropriate query separator ? or & first, depending on whether there is a query parameter in the URL already. The call will return one event (which you can ignore) and a next link you'll need later.
Sleep and loop, following the link at next.href after processing each batch of events. It will return immediately whether or not there are new events waiting. Follow the advice in downloading the entire event database about keeping track of your position if you might encounter a problem writing events mid-batch.
Receiving new events as they occur, starting in the past
GET /api
Follow the link at features.events.events.href ↪ appending the query parameter after=2021-05-08Z (or whatever timestamp is appropriate).
Process the events you receive in that call.
Sleep to reduce load on the server.
Loop, following the link at updates.href after processing each batch of events. It will return immediately if there are new events waiting, or it will block until new events arrive. Follow the advice in downloading the entire event database about keeping track of your position if you might encounter a problem writing events mid-batch. Note that that advises using the next field, but you will be using the updates field, which is the same thing in long-poll form.
Searching for events related to particular cardholders
GET /api

Follow the link at features.items.items.href ↪, adding name="your_cardholder_name"&type=1 to the query after the appropriate separator (? or &).

The 'items' controller necessary for that step is available with the RESTEvents licence. If you also have the RESTCardholders licence you could use the link at features.cardholders.cardholders.href ↪ instead, adding a separator and name="your_cardholder_name".

In either case, remove the quotes if you want a substring search and can handle more than one cardholder in the results.

Extract the item ID of your cardholder or cardholders from that page, repeating as necessary for additional cardholders.

Follow the link on the /api page at features.events.events.href ↪ appending the separator and cardholder=XX or cardholder=XX,YY,ZZ with the cardholders' IDs.

To further improve the efficiency of your search, filter by event types and a time range.

Searching for events that indicate location or movement
If your server is running 8.60 or earlier, use the search filter type=20001,20002,20003,20047,20107,42415. That is not a complete list of movement event types but it includes the popular ones.

If your server is running 8.70-8.90 inclusive, give your operator the 'View cardholder events' privilege instead of 'View events'. Then request all events, and the server will only send you movements.

If your server is running 9.00 or later, the 'View cardholder events' privilege is still an excellent idea but you can also use the search filter type=hasLocation&fields=location (with a leading ? or & of course, and more fields if you need them). That will limit the results to location events. Then look at the type field inside the location block:

If you are simply after a person's location, use the afterLocation field inside the location block. That could be an access zone or division if they moved or attempted to move, or a workstation or alarms terminal if they logged into one of those. The canonicalTypeName field inside the block will tell you what kind of item it is. Future versions of Command Centre or customisations might add more.

If you are after movements, which happen when a door grants a person access or an operator moves them on a tag board or via the API, and happen to some types of visitors when their host moves, ignore all events except those that have a location.type of moved. Look at the afterLocation to see where they landed. It will be an access zone or (for departing visitors) a reception. There will also be a beforeLocation if the door had two zones configured on it, in case you're interested in where they came from. The canonicalTypeName field in both blocks will tell you what kind of item it is.

If you are after denials, which happen when a person authenticates but fails the access check, look at the events that have a location.type of denied. Like 'moved'-type events, location.afterLocation will show where they were. The difference is that with 'denied'-type events, they started there too.

Each of those fields is covered in the schema definition.

Be aware that the events you receive are limited to those with a source item that is in a division in which your operator has a privilege that allows viewing events. In 9.00 those privileges are 'View Events' and 'View Cardholder Events', and of course 'Advanced User'. That makes this API unsuitable for handling emergencies such as evacuations if your operator's view is limited: if your operator does not have the privilege to view events generated by a particular door, this API will not tell you about movements through that door.

Searching for events coming from other items
Events such as 'access granted' and 'zone count maximum' come from doors and access zones. To search for them, follow the same process as the previous use case (getting cardholder events) but use the source filter parameter instead of cardholder. It will limit the results to just those events that came from the items you gave in the query. If you have the RESTStatus licence (added in v8.00), you can use that API to search for access zones, alarm zones, fence zones, macros, outputs, doors, and (in 8.10) inputs. If you do not, or if your source is not one of those types, use the items API with a suitable type filter.

You can also use the type parameter to limit the events to particular event types. By doing this you can (for example) subscribe to 'access granted' events from a collection of doors.

New in v8.70, you can also filter by relatedItem. Use this to find events related to item or items regardless of the type.

Creating a new event
GET /api
POST to the link at features.events.events.href ↪.
There are some rules around creating events, so you should first have a careful read of the POST documentation.

Listing all event types
GET /api
GET the link at features.events.eventGroups
That will return all event types in their groups.

Licensing
The GETs that collect alarms and events require the RESTEvents licence.

The POST that creates events requires RESTCreateEvents.

The GET to collect event types requires RESTEvents or RESTCreateEvents.

Efficiency tips
Use search parameters to reduce the filtering burden on the server.

When downloading a significant number of events, leave top at 1000 or more, provided your client can handle results over a megabyte (events are around 1 KB each). Performance tests have shown that throughput decreases dramatically if top is too low.

If using 8.40 or later, use its field query parameter to cut back on the fields the server sends to you. Not only will it save bandwidth, but it will save the server looking up all those values and serialising them for you.

If you are using the updates link to keep up to date with events, sleep between calls for as long as your requirements allow. Doing that will improve the likelihood of your collecting more than one event when it is busy.
For example, if Command Centre is generating ten events per second and you do not sleep between REST calls, you will be calling updates ten times per second for one event each time. However if you sleep for two seconds after each call you will receive 20 events at a time, saving CPU and I/O.

Events
Add an event
POST /api/events
Use this method to create an event in Command Centre v8.10 or later.

Do not code this URL into your application. Take it from events.events.href in the results of GET /api.

Each field has particular rules and has its own effects on the event and subsequent reports, and misconfiguration (such as inadvertently causing a macro to run itself) can land you in real trouble, so have a good look at the documentation below and the example POST body.

Events are immutable: you cannot PATCH or DELETE them after you create them.

Usable event types
You must supply an event type. 8.10 ships with 30 external event types you can use, each in its own event type group.

You can create 970 of your own event types using the External Event Type Configuration Utility, a separate Windows application that lets you create external event types and make them appear on items' Event Response and Alarm Instructions tabs in the Configuration Client. You will find the release note for that utility in the Documentation folder on the installation media.

1000 event types sounds like a lot, but be aware that you cannot delete event types, and the only thing you can modify on an existing event type is its name. Their event type group and item types are permanent once you save them from the utility. Please plan carefully, and take backups!

Usable source items
Every event needs a source item. You can let the server pick one for you (it will use the REST Client item identified by your API key) or you can use any item that has an 'Event Response' tab in its Configuration Client window (which is most of them). Our existing integrations use items like doors, external system items, and cameras.

If you allow the server to default to your REST client item as the source, or if specify it yourself, your operator must have 'Create events and alarms' in at least one division for the call to succeed. Any division will do. Otherwise you'll receive a 403.

If you specify a source item to a server running 8.90 or later and that item is not your REST Client item, your operator must have the 'Create events and alarms' privilege in that item's division. In older versions it was enough for your operator to have that privilege in any division.

Cardholders cannot be event sources because they do not have an 'Event Response' tab, but...

Related items
Along with a source, you can link other items to the events you create. If you link a cardholder, for example, your events will show on an activity report generated for that cardholder. To link an item your operator must have a privilege that allows viewing it ('View cardholders' for cardholders or 'View site' for most other item types).

Action plans
An event can fire an action plan, which will

set the priority if the REST client did not set one in the body of the POST, and
run a macro on the server.
Macros are extremely powerful, and a thorough treatment requires more room than we have, so it is sufficient to say that you should not aim for a dramatic first test. A good first result is to turn on a virtual output made for the purpose. Just make sure that it will not trigger another macro because it is possible to create loops, causing havoc.

You do not pick an action plan to run when you POST your event. Command Centre does that in three steps:

The server looks at the configuration of the source item in the Event Response tab of its property page in the Configuration Client. If there is an entry for the event type that is not "use default", Command Centre will fire that action plan and skip the next steps. In versions older than 8.30 the control is per event group, not per event.
Since the server did not find an action plan on the item, it tries its alarm zone. Specifically, the Event Defaults tab of the alarm zone's property page in the Configuration Client. Again the control is per event group not per event type in older versions. If the event's action plan is not 'use default', the server fires that action plan and goes no further.
Since the server did not find an action plan on the item or its alarm zone, it looks at the configuration in the Event Defaults tab of the server properties. There is always an action plan there, even if it does nothing more than set the priority of the event.
Once it has found the action plan to run, the server will assign the event its priority (if you did not specify a priority yourself) and run the macro if there is one on the 'Command Centre' tab of the action plan's property page in the Configuration Client. The server will not use the configuration from the other tabs.

It is not possible to submit an event with priority zero, but it is possible to submit an event with no priority, and have the action plan assign it priority zero. This will run the macro on the action plan then drop the event before it reaches the database.

Alarm instructions
Alarm instructions are marked-up text fields that Command Centre presents to security personnel when events occur. Picking an alarm instruction to use follows the same decision path as picking an action plan: Command Centre looks at the configuration of the source item first, and finding nothing there will turn to the source item's alarm zone, and finally to the server properties. If all three are unset the operator will not receive any special instructions.

So, what an operator sees when your event arrives on their board depends on the priority and the event source.

When events become alarms
After the server has established an event's priority, either from the body you POSTed or the action plan, it looks at the Event Priorities tab of the server properties. There is a slider there that sets the level above which an event becomes an alarm. By default it is set to two, meaning that any event with a priority of two or higher will appear as an alarm.

Event POST body
You can specify many things on an event but the only mandatory field is the type. When you are developing, start with just that.

Request Example
{
  "type": {
    "href": "https://localhost:8904/api/events/types/4000"
  },
  "eventType": {
    "href": "https://localhost:8904/api/events/types/4000"
  },
  "source": {
    "href": "https://localhost:8904/api/doors/745"
  },
  "priority": 2,
  "time": "2019-02-21T14:55:00Z",
  "message": "Glass break detected in southwest sauna",
  "details": "",
  "cardholder": {
    "href": "https://localhost:8904/api/cardholders/325"
  },
  "operator": {
    "href": "https://localhost:8904/api/cardholders/5398"
  },
  "entryAccessZone": {
    "href": "https://localhost:8904/api/access_zones/333"
  },
  "accessGroup": {
    "href": "https://localhost:8904/api/access_groups/352"
  },
  "lockerBank": {
    "href": "https://localhost:8904/api/locker_banks/4566"
  },
  "locker": {
    "href": "https://localhost:8904/api/lockers/3456"
  },
  "door": {
    "href": "https://localhost:8904/api/doors/745"
  }
}
201 Created
Success.

204 No Content
Success with no event created, probably because the priority in the action plan was zero.

400 Bad Request
The parameters are invalid. Check the body of the response for an error message.

8.90 and later will reject the POST with a 400 if your event specifies a source item that is not your REST Client item and your operator does not have 'Create Events and Alarms' on its division.

403 Forbidden
The site does not have the RESTCreateEvents licence (in which case the body of the response will say so) or operator does not have the 'Create Events and Alarms' privilege.

Response Headers (201 Created)
location	
The href of the new event.

string (url)
Events
Search events
GET /api/events
This returns the next batch of events matching the supplied filters starting at the beginning of the database, or at the time specified by the after parameter.

For its correct use in various scenarios, see the use cases.

By default the result will contain no more than 1000 events; for efficient transfer of large numbers of events you should increase this with the top parameter in the request URL.

Each response will contain a next and an updates link. Following the next link will return the next batch of events, or an empty list if there are no more available. Following the updates link will also return immediately if more events are available, but if there are none, it becomes a long poll. It will will block until an event is available that matches the specified filters, or a timeout passes.

Do not code this URL into your application. Take it from events.events.href in the results of GET /api.

top
in query
integer 1 ≤ x ≤ 10000 1000
Sets the maximum number of events to return per page.

after
in query
string (date-time)
Restricts events to those that occurred at or after this time.

The server accepts extended ISO-8601 time stamp formats. There must be hyphen separators in the date and colons in the time, and a T separating the two.

For predictable results you should also add a timezone specifier. A Z means UTC and is recommended, but +hh, +hhmm, and +hh:mm also work. Note that you need to encode plus-signs as %2b in a URL.

If you omit the time, the server will assume midnight. If you omit just the seconds, the server will take it to the top of the minute, :00.

before
in query
string (date-time)
Restricts events to those that occurred before this time. Events that occurred at this exact time (to the second) will not appear in the results. For example, to collect all events that occurred on 1 January 2017, use after=2017-01-01Z,before=2017-01-02Z. You will not receive any events from 2 January.

The after parameter (above) describes the time stamp formats that the server accepts.

source
in query
string
Restricts events to those whose source item has this ID. Separate multiple IDs with commas. Use the items API to search Command Centre's items.

type
in query
string
Restricts events to those whose type has this ID. Separate multiple IDs with commas.

The results may include other event types if you also use the group parameter, below.

Use the event groups call to see all event types. The IDs you need for this query parameter are strings but look like small integers. That call shows the event types along with the groups they are in, so do not confuse event types with event groups. They are separated in the results of the API call, but as a general rule you can tell them apart by their integer value: event group IDs are in the hundreds, while most event type IDs are in the thousands.

In version 9.00 and later use the special value hasLocation to include all event types that can reveal a cardholder's location or access zone. In 9.00 it is a set of about 80 event types; it may be more in future versions. If you use hasLocation you probably also want to ask for the location block using the fields query parameter.

Do not send type=hasLocation to a server running 8.90 or earlier: it will reject it.

group
in query
string
Restricts events to those with this event group ID. Separate multiple IDs with commas.

group is ORed with type. In fact it is shorthand for type= followed by the IDs of all the event types in the event type groups you list, and the search uses the union of the two lists.

Use the event groups call to see all event types and groups.

cardholder
in query
string
Restricts events to those associated with the cardholder that has this Command Centre ID. Separate multiple IDs with commas.

Example: cardholder=325

division
in query
string
Restricts events to those in these divisions or their descendants. Separate IDs with commas.

Example: division=201,330

A more secure option for limiting your client's visibility is to set the operator's privileges so that it only has access to those divisions.

directDivision
in query
string
Restricts events to those whose division is in this list. Unlike division=, it does not follow ancestry. Separate IDs with commas.

Example: division=2,101.

relatedItem
in query
string
Restrict events to those associated with the item that has this Command Centre ID. Separate multiple IDs with commas.

Example: relatedItem=3,102

fields
in query
string  defaults, details, cardholder.pdf_*, href, id, serverDisplayName, time, message, occurrences, priority, alarm, operator, source, group, type, eventType, division, cardholder, entryAccessZone, exitAccessZone, door, accessGroup, card, modifiedItem, lastOccurrenceTime, previous, next, updates, location 
Sets the fields you want in your results.

This is the list of valid fields for 8.40, but it is different for older server versions, so check with the topic on event field specifiers.

previous
in query
boolean false
Returns the newest events rather than the oldest. Without this option the API will return events starting from the epoch, but if you set it to true the server will return the most recent events, the last of which will be the latest to arrive at the server.

In both cases you can move backward and forward in arrival time with the 'next' and 'previous' links.

pos
in query
integer x ≥ 0
Restricts events to those with event IDs greater than this parameter (or less than and including, if you set previous=true).

INTERNAL USE ONLY. This is how Command Centre tracks the events you have seen already. Do not set it yourself. Retain the 'next' or 'updates' link in your application instead.

200 OK
Event search
Success

403 Forbidden
The site does not have a RESTEvents licence.

Response Example (200 OK)
{
  "events": [
    {
      "href": "https://localhost:8904/api/events/61320",
      "id": "61320",
      "serverDisplayName": "ruatoria.satellite.int",
      "time": "2016-02-18T19:21:52Z",
      "message": "Operator logon failed for FT Workstation on GNZ-PC1439",
      "occurrences": 2,
      "priority": 3,
      "alarm": {
        "state": "unacknowledged",
        "href": "https://localhost:8904/api/alarms/61320"
      },
      "operator": {
        "href": "https://localhost:8904/api/cardholders/325",
        "name": "Chong, Marc"
      },
      "source": {
        "id": "321",
        "name": "FT Workstation on GNZ-PC1439",
        "href": "https://localhost:8904/api/items/321"
      },
      "group": {
        "id": "35",
        "name": "Invalid Logon"
      },
      "type": {
        "id": "601",
        "name": "Operator logon failed"
      },
      "eventType": {
        "id": "601",
        "name": "Operator logon failed"
      },
      "division": {
        "id": "2",
        "href": "https://localhost:8904/api/divisions/2",
        "name": "Root division"
      },
      "cardholder": {
        "href": "https://localhost:8904/api/cardholders/325",
        "id": "325",
        "name": "Bruce, Jennifer",
        "firstName": "Jennifer",
        "lastName": "Caitlin"
      },
      "entryAccessZone": {
        "href": "https://localhost:8904/api/access_zones/333",
        "name": "Brookwood showroom",
        "id": "333"
      },
      "exitAccessZone": {
        "href": "https://localhost:8904/api/access_zones/913",
        "name": "Compressor room",
        "id": "913"
      },
      "door": {
        "href": "https://localhost:8904/api/doors/745",
        "name": "Main hoist door"
      },
      "accessGroup": {
        "href": "https://localhost:8904/api/access_groups/352"
      },
      "card": {
        "facilityCode": "A12345",
        "number": "78745",
        "issueLevel": 1
      },
      "modifiedItem": {
        "href": "https://localhost:8904/api/cardholders/325",
        "type": {
          "id": "1",
          "name": "Cardholder"
        }
      },
      "next": {
        "href": "https://localhost:8904/api/events?pos=61320"
      },
      "previous": {
        "href": "https://localhost:8904/api/events?pos=61320&previous=True"
      },
      "updates": {
        "href": "https://localhost:8904/api/events/updates?pos=61320"
      }
    }
  ],
  "previous": {
    "href": "https://localhost:8904/api/events/next?previous=True&pos=61320"
  },
  "next": {
    "href": "https://localhost:8904/api/events/next?pos=61320"
  },
  "updates": {
    "href": "https://localhost:8904/api/events/updates?pos=61320"
  }
}
Events
Get new events (or wait)
GET /api/events/updates
Poll this link to receive new events that match the specified filters. If there are none ready, the call will block until one arrives or a deadline passes.

For its correct use in various scenarios, see the use cases. In particular, sleep between calls to reduce load on the server.

The way this call picks events is very different from /api/events:

If you follow the link at events.updates.href in the results of GET /api, you will receive the first events that arrive after you make the call and that meet your other search criteria.

If you follow the updates link from the results of GET /api/events or GET /api/events/updates, the link will contain a bookmark in the pos parameter that causes the server to return the first events that arrive or arrived after that bookmark (and that meet your search criteria, of course).

The response will contain an updates link back to the same API call with a new bookmark that will cause it to return the next page of results.

Each response will also contain a next link that will take you to the non-blocking version of the call at /api/events.

However long you wait between calls, following a next or updates link will always return the first events that arrived after your previous call.

Do not code this URL into your application. Take it from events.updates.href in the results of GET /api, or from updates in the results of GET /api/events.

top
in query
integer 1 ≤ x ≤ 10000
Sets the maximum number of events to return per page.

deadline
in query
integer 1 ≤ x ≤ 86400
Sets the number of seconds to wait for an event, if none are ready when you make the call. If none arrive before this number of seconds pass the result set will be empty. If not specified, a default will apply.

after
in query
string (date-time)
Removes events that occurred before this time from the result set. It must be an ISO-8601 date or date-time with a timezone.

It is unlikely you will add this parameter to GET /api/events/updates. However it is a very useful parameter to /api/events, and it will pass from there into the updates URL that that call returns. In that case it has no effect; you can leave it there.

This will not push the start time of the search into the past. As described above, the search will start at the time of the call or after the last event in a previous result set depending on the pos parameter. The after parameter reduces the results to those events that arrived after the start time of the search and occurred after the after timestamp. Note that an event's arrival time can be different from its occurrence time.

before
in query
string (date-time)
Removes events that occurred at or after this time from the result set. It must be an ISO-8601 date or date-time string with a timezone.

If no events arrive with an occurrence time earlier than this parameter, the call will eventually time out.

It is unlikely you will want this parameter in a call to GET /api/events/updates. It puts an end-date on the search, which is a very odd thing to do on a call intended to keep the caller up to date with events as they arrive. If you find yourself using it, you may wish to reconsider your approach.

If you only wish to receive events up to a point in history, use the before parameter on /api/events, following its next block in a loop until you get an empty result. If you only wish to receive events up to a point in the future, use /api/events again but loop until you receive an event with a date beyond your stopping point.

source
in query
string
Restricts events to those whose source item has this ID. Separate multiple IDs with commas. Use /api/items to search Command Centre's items.

type
in query
string
Restricts events to those with this event type ID. Separate multiple IDs with commas.

Use /api/events/groups to see all of Command Centre's event types and groups. Event types names and IDs rarely change, but the ID is the more stable of the two. Therefore it is probably safer to use that API for reference then hard-code the event type IDs you find there into your application.

group
in query
string
Restricts events to those with this event group ID. Separate multiple IDs with commas.

The documentation for /api/events/groups advises when to filter for event groups instead of event types.

Like event types, hard-coding the ID into your application is probably stabler (and definitely simpler) than searching for it at runtime.

cardholder
in query
string
Restricts events to those associated with the cardholder with this Command Centre ID. Separate multiple IDs with commas.

Example: cardholder=325

division
in query
string
Restricts events to those in the division with this ID and its descendant divisions. Separate multiple IDs with commas.

Example: division=2,101

fields
in query
string  defaults, details, cardholder.pdf_*, href, id, serverDisplayName, time, message, occurrences, priority, alarm, operator, source, group, type, eventType, division, cardholder, entryAccessZone, exitAccessZone, door, accessGroup, card, modifiedItem, lastOccurrenceTime, previous, next, updates, location 
Sets the fields you want in your results.

This is the list of valid fields for 8.40, but it is different for older server versions, so check with the topic on event field specifiers.

pos
in query
integer x ≥ 0
Restricts events to those with event IDs greater than this parameter.

INTERNAL USE ONLY. Retain the 'next' or 'updates' link in your application instead. This is how Command Centre tracks the events you have seen already. Do not set it yourself.

200 OK
Event search
Success

403 Forbidden
The site does not have a RESTEvents licence.

Response Example (200 OK)
{
  "events": [
    {
      "href": "https://localhost:8904/api/events/61320",
      "id": "61320",
      "serverDisplayName": "ruatoria.satellite.int",
      "time": "2016-02-18T19:21:52Z",
      "message": "Operator logon failed for FT Workstation on GNZ-PC1439",
      "occurrences": 2,
      "priority": 3,
      "alarm": {
        "state": "unacknowledged",
        "href": "https://localhost:8904/api/alarms/61320"
      },
      "operator": {
        "href": "https://localhost:8904/api/cardholders/325",
        "name": "Chong, Marc"
      },
      "source": {
        "id": "321",
        "name": "FT Workstation on GNZ-PC1439",
        "href": "https://localhost:8904/api/items/321"
      },
      "group": {
        "id": "35",
        "name": "Invalid Logon"
      },
      "type": {
        "id": "601",
        "name": "Operator logon failed"
      },
      "eventType": {
        "id": "601",
        "name": "Operator logon failed"
      },
      "division": {
        "id": "2",
        "href": "https://localhost:8904/api/divisions/2",
        "name": "Root division"
      },
      "cardholder": {
        "href": "https://localhost:8904/api/cardholders/325",
        "id": "325",
        "name": "Bruce, Jennifer",
        "firstName": "Jennifer",
        "lastName": "Caitlin"
      },
      "entryAccessZone": {
        "href": "https://localhost:8904/api/access_zones/333",
        "name": "Brookwood showroom",
        "id": "333"
      },
      "exitAccessZone": {
        "href": "https://localhost:8904/api/access_zones/913",
        "name": "Compressor room",
        "id": "913"
      },
      "door": {
        "href": "https://localhost:8904/api/doors/745",
        "name": "Main hoist door"
      },
      "accessGroup": {
        "href": "https://localhost:8904/api/access_groups/352"
      },
      "card": {
        "facilityCode": "A12345",
        "number": "78745",
        "issueLevel": 1
      },
      "modifiedItem": {
        "href": "https://localhost:8904/api/cardholders/325",
        "type": {
          "id": "1",
          "name": "Cardholder"
        }
      },
      "next": {
        "href": "https://localhost:8904/api/events?pos=61320"
      },
      "previous": {
        "href": "https://localhost:8904/api/events?pos=61320&previous=True"
      },
      "updates": {
        "href": "https://localhost:8904/api/events/updates?pos=61320"
      }
    }
  ],
  "previous": {
    "href": "https://localhost:8904/api/events/next?previous=True&pos=61320"
  },
  "next": {
    "href": "https://localhost:8904/api/events/next?pos=61320"
  },
  "updates": {
    "href": "https://localhost:8904/api/events/updates?pos=61320"
  }
}
Events
Get details of an event
GET /api/events/{id}
Full details for an event. You could follow the href in the event summary to get here, but if you are running 8.20 or later you could just use the fields parameter to add the details field to the summary results for the same result.

id
in path
string
The ID of the event.

fields
in query
string  defaults, details, cardholder.pdf_*, href, id, serverDisplayName, time, message, occurrences, priority, alarm, operator, source, group, type, eventType, division, cardholder, entryAccessZone, exitAccessZone, door, accessGroup, card, modifiedItem, lastOccurrenceTime, previous, next, updates, location 
Sets the fields you want in your results.

This is the list of valid fields for 8.40, but it is different for older server versions, so check with the topic on event field specifiers.

200 OK
Event detail
Success

403 Forbidden
The site does not have a RESTEvents licence.

404 Not Found
That is not the URL of an event, or it is but you do not have privileges to read events in its division.

Response Example (200 OK)
{
  "href": "https://localhost:8904/api/events/61320",
  "id": "61320",
  "serverDisplayName": "ruatoria.satellite.int",
  "time": "2016-02-18T19:21:52Z",
  "message": "Operator logon failed for FT Workstation on GNZ-PC1439",
  "occurrences": 2,
  "priority": 3,
  "alarm": {
    "state": "unacknowledged",
    "href": "https://localhost:8904/api/alarms/61320"
  },
  "operator": {
    "href": "https://localhost:8904/api/cardholders/325",
    "name": "Chong, Marc"
  },
  "source": {
    "id": "321",
    "name": "FT Workstation on GNZ-PC1439",
    "href": "https://localhost:8904/api/items/321"
  },
  "group": {
    "id": "35",
    "name": "Invalid Logon"
  },
  "type": {
    "id": "601",
    "name": "Operator logon failed"
  },
  "eventType": {
    "id": "601",
    "name": "Operator logon failed"
  },
  "division": {
    "id": "2",
    "href": "https://localhost:8904/api/divisions/2",
    "name": "Root division"
  },
  "cardholder": {
    "href": "https://localhost:8904/api/cardholders/325",
    "id": "325",
    "name": "Bruce, Jennifer",
    "firstName": "Jennifer",
    "lastName": "Caitlin"
  },
  "entryAccessZone": {
    "href": "https://localhost:8904/api/access_zones/333",
    "name": "Brookwood showroom",
    "id": "333"
  },
  "exitAccessZone": {
    "href": "https://localhost:8904/api/access_zones/913",
    "name": "Compressor room",
    "id": "913"
  },
  "door": {
    "href": "https://localhost:8904/api/doors/745",
    "name": "Main hoist door"
  },
  "accessGroup": {
    "href": "https://localhost:8904/api/access_groups/352"
  },
  "card": {
    "facilityCode": "A12345",
    "number": "78745",
    "issueLevel": 1
  },
  "modifiedItem": {
    "href": "https://localhost:8904/api/cardholders/325",
    "type": {
      "id": "1",
      "name": "Cardholder"
    }
  },
  "next": {
    "href": "https://localhost:8904/api/events?pos=61320"
  },
  "previous": {
    "href": "https://localhost:8904/api/events?pos=61320&previous=True"
  },
  "updates": {
    "href": "https://localhost:8904/api/events/updates?pos=61320"
  },
  "lastOccurrenceTime": "2016-02-18T19:21:59Z",
  "details": "Originating IP address: 192.168.2.3",
  "location": {
    "type": "moved",
    "cardholder": {
      "name": "Jackson",
      "href": "https://localhost:8904/cardholders/325"
    },
    "beforeLocation": {
      "href": "https://localhost:8904/api/access_zones/333",
      "name": "Lvl 1 lift lobby",
      "canonicalTypeName": "accesszone"
    },
    "afterLocation": {
      "outside": true
    }
  }
}
Events
List event types
GET /api/events/groups
Retrieves the list of event type groups and the event types within those groups. Useful for obtaining IDs to use in event filters. Command Centre ships with about 1000 event types divided into about 150 groups. Each event type is in one group.

A site may rename the 30 groups dedicated to external event types—that is, event types for the site's own use—and may create another 970 event types for them.

The results of this query vary with:

the server version,
extra Gallagher software installed on the site, and
changes to external event groups and types made by the customer.
Event type identifiers do not often change between Command Centre versions, but the types in each group do. Therefore if you choose to use groups in filters rather than types you may find that your filter catches more or fewer event types after a Command Centre upgrade. That may be desirable if, for example, you are intested in a class of event and you want to grow with CC as it grows new features. If you want to catch all 'access denied's, for example. We frequently add new varieties of 'access denied'. If you do not want that, use types rather than groups.

Do not code this URL into your application. Take it from events.eventGroups.href in the results of GET /api.

200 OK
Event groups
Success

403 Forbidden
The site does not have the RESTEvents or RESTCreateEvents licence.

Response Example (200 OK)
{
  "eventGroups": [
    {
      "id": 35,
      "name": "Invalid Logon",
      "eventTypes": [
        {
          "href": "https://localhost:8904/api/events/types/601",
          "id": "601",
          "name": "Operator logon failed"
        },
        {
          "href": "https://localhost:8904/api/events/types/20065",
          "id": "20065",
          "name": "Terminal: Invalid User Code"
        },
        {
          "href": "https://localhost:8904/api/events/types/23052",
          "id": "23052",
          "name": "Wrong Code only Code"
        }
      ]
    }
  ]
}
Items
These methods let you find items for your search filters and events, and monitor their states. They return all items, including those that this API does not yet support in depth, and items added by customisations. However because they do not have deep knowledge of item types they can only give you the most basic fields.

Searching for items
Use the search methods when you are building a filter for an event search or a status subscription and need the ID of an item or an item type, or when you are creating an event and need an href to use as the event source.

To find an item, pass a substring of its name to the link at features.items.items.href in the results of a call to /api. If you are sure of its name, place the name inside " quotes, and it will use a full string match. Both types of search are case-insensitive.

To limit the search to items of a particular type, first get the ID of the type you are after using the link at features.items.itemTypes.href in the results of GET /api. Add that ID as the type parameter to the call above. You can specify multiple item types if, for example, you are interested in all the different kinds of doors.

For example, if you were after a list of divisions, following the instructions above on the current versions of Command Centre would produce the URL /api/items?type=15.

Status subscriptions
The item-specific APIs monitor only one item at a time, and are therefore not suitable for watching large collections. If you have CC version 8.30 or later, you should use the status subscription methods instead. We have tested subscriptions of 1000 items without noticing undue strain on the server. While the calls do not impose an upper bound on that we suggest keeping a watchful eye on the performance of the overall system if you go much higher.

The basic operations for monitoring item states is:

Get all the IDs of the items you wish to monitor. You can do that using this API's own search with a name, division, or type parameter. If you're searching by division you'll need a division ID which (slightly recursively) is best found by using the same search function filtering for just divisions. If you're searching by type you'll need a type ID.

POST to create a subscription. Your program should get the URL from items.updates.href in the results of GET /api.

Take the current state of your items from the results of that call. If that's all you need, terrific.

But if you want to monitor their state, GET the next.href link that came in the results of the POST. The call will block until one of your monitored items changes state. When it returns, the results will be in the same format as the result of the POST, including the next link.

Loop.

Licensing
Every REST licence enables the items controller: RESTEvents, RESTCreateEvents, RESTCardholders, RESTStatus, and RESTOverrides.

Items
Search items
GET /api/items
This returns a batch of items matching the applied filters. By default, each page will contain up to 1000 items although this can be changed by setting the top parameter in the request URL.

You will only receive items for which the REST operator has the necessary privilege. To view PDFs, for example, the operator must have the 'View Personal Data Definitions' privilege.

If more items are available, the response will contain a next link. Following that will return the next batch of items.

Items will be in ID order unless you change it with sort.

Do not code this URL into your application. Take it from items.items.href in the results of GET /api.

name
in query
string
Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

division
in query
string[]
Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

type
in query
string
Only returns items that are of a type with this ID. In versions up to 8.30 you could only specify one, but in 8.40 and later this can be a comma-separated list.

top
in query
integer x ≥ 1 100
Sets the maximum number of items to return per page.

sort
in query
string  id, name, -id, -name 
Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. This is known as "page drift." Sorting by ID does not carry that risk.
Following a next link is dramatically quicker when sorting by ID.
We strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

The server silently ignores anything except the options listed here.

fields
in query
string  href, id, name, type, division, serverDisplayName, notes 
Return these fields in the search results. The values you can list are the same as the field names in the details page. Using it you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

The string must not contain any spaces. Just alphanumerics, underscores, commas, and dots.

Treat the string matches as case-sensitive.

200 OK
Item search
Success

403 Forbidden
The site does not have a REST licence.

Response Example (200 OK)
{
  "results": [
    {
      "id": "325",
      "name": "Brick, Eva",
      "type": {
        "id": "1",
        "name": "Cardholder",
        "canonicalTypeName": "cardholder"
      }
    },
    {
      "id": "2707",
      "name": "Brewer, Amy",
      "type": {
        "id": "1",
        "name": "Cardholder",
        "canonicalTypeName": "cardholder"
      }
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/items?pos=2"
  }
}
Items
Get details of an item
GET /api/items/{id}
This returns some basic fields for one item. It returns the same information as the item search plus the item's division.

Added in 8.40.

id
in path
string
The ID of the item.

fields
in query
string  href, id, name, type, division, serverDisplayName, notes 
Return these fields in the search results. The values you can list are the same as the field names in the details page. Using it you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

The string must not contain any spaces. Just alphanumerics, underscores, commas, and dots.

Treat the string matches as case-sensitive.

200 OK
Item detail
Success

404 Not Found
That is not the href of an item. At least not one that your operator has the privilege to view.

Response Example (200 OK)
{
  "id": "325",
  "name": "Brick, Eva",
  "type": {
    "id": "1",
    "name": "Cardholder",
    "canonicalTypeName": "cardholder"
  },
  "serverDisplayName": "ruatoria.satellite.int",
  "notes": "Multi-line text...",
  "href": "string",
  "division": {
    "id": "2",
    "href": "https://localhost:8904/api/divisions/2"
  }
}
Items
List item types
GET /api/items/types
Retrieves the list of all item types in the Command Centre system. There are about 200. This is useful for obtaining type IDs to use in item search filters and (in 9.00) the canonical item type names.

Note that some item types have a blank name. These types are vestigial: disregard them.

Do not code this URL into your application. Take it from items.itemTypes.href in the results of GET /api.

200 OK
Item types
Success

403 Forbidden
The site does not have a REST licence.

Response Example (200 OK)
{
  "itemTypes": [
    {
      "id": "1",
      "name": "Cardholder",
      "canonicalTypeName": "cardholder"
    },
    {
      "id": "2",
      "name": "Access Group",
      "canonicalTypeName": "accessgroup"
    }
  ]
}
Items
Subscribe to status updates
POST /api/items/updates
Creates a subscription to status changes. You POST a list of item IDs and the server returns the status flags of those items plus a link. When you GET that link some time later the server will return the items that changed state between the two calls.

If you do not GET the link within thirty seconds of the POST returning, the server will drop your subscription and free up the resources it had allocated to servicing it.

Your operator must have view privileges on every item in the subscription.

The subscription notices changes in state, not in configuration, so an operator modifying an item will not cause anything to come out of this API unless the change in configuration also causes a change in state.

Because this call returns the status of items, this call requires the RESTStatus licence.

Added in 8.30.

Item update subscription
The body of the POST needs to contain a list of item IDs in an array called itemIds. Even though they look like small integers, these IDs are actually strings so don't forget the quotes.

Request Example
{
  "itemIds": [
    "508",
    "526"
  ]
}
200 OK
Item update
Success

400 Bad Request
The server could not parse the request body. Check your JSON.

401 Unauthorized
The operator does not have privilege to view the monitored items. This will change to 403 in a future version of Command Centre.

403 Forbidden
The site does not have the RESTStatus licence.

Response Example (200 OK)
{
  "updates": [
    {
      "id": "508",
      "status": "Controller offline. 62 message(s) pending.",
      "statusText": "Controller offline.\r\n62 message(s) pending.",
      "statusFlags": [
        "controllerOffline"
      ]
    },
    {
      "id": "526",
      "status": "Disarmed.",
      "statusText": "Disarmed.",
      "statusFlags": [
        "disarmed"
      ]
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/items/updates?bookmark=3ec613a1-de01c6e_0"
  }
}
Items
Retrieve status updates
GET /api/items/updates
Collects status updates from a subscription created using the /api/items/updates POST.

This is a long poll, so if there are no updates waiting when you make the call it will block until some arrive or a timeout passes (about 50 seconds).

If you receive a 404 from this call it means that too much time has passed since the server sent the link, or the server has restarted. In either case the server will have dropped your subscription. You will need to create a new one with a fresh POST.

Therefore your loop can be:

Create a subscription with a POST.
Process the statuses in the results, if there are any.
Wait a second or two to avoid tight loops.
GET the link from the results. It may take up to a minute to respond.
If 404, go to 1.
Go to 2.
...plus the necessary exception handling, of course.

Added in 8.30.

Note that the first time you make this GET request it will return all the activity that came back from the POST. There are other cases where the GET might return data you have seen already (when an override is sent to an item, or some aspect of an item changes that your operator does not have the privilege to view, for example). These two behaviours are not harmful, but also not particularly helpful, so future versions may differ.

bookmark
in query
string
Identifies your subscription and your position in the change list. You do not need to set this parameter: it will be in the link that the server sends back to you.

200 OK
Item update
Success

403 Forbidden
The site does not have a REST licence.

404 Not Found
The subscription does not exist, which probably means you waited too long between calls.

Response Example (200 OK)
{
  "updates": [
    {
      "id": "508",
      "status": "Controller offline. 62 message(s) pending.",
      "statusText": "Controller offline.\r\n62 message(s) pending.",
      "statusFlags": [
        "controllerOffline"
      ]
    },
    {
      "id": "526",
      "status": "Disarmed.",
      "statusText": "Disarmed.",
      "statusFlags": [
        "disarmed"
      ]
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/items/updates?bookmark=3ec613a1-de01c6e_0"
  }
}
Schema Definitions
Alarm search: object
An array of alarm summaries, and either a 'next' or an 'updates' link you should follow to retrieve more alarms.

alarms:  Alarm summary 
An array of alarm summaries.

Alarm summary
next: object
The link to the next page of alarms. Missing if you have retrieved all the current alarms.

href: string
updates: object
The link to follow to long-poll for alarm changes. Missing if you have not yet retrieved all the current alarms.

href: string
Example
{
  "alarms": [
    {
      "href": "https://localhost:8904/api/alarms/10135",
      "id": "10135",
      "time": "2016-02-18T19:21:52Z",
      "message": "External bulk loading bay door has been forced",
      "source": {
        "id": "1321",
        "name": "External bulk loading bay door",
        "href": "https://localhost:8904/api/doors/1321"
      },
      "type": "Forced door",
      "eventType": {
        "id": "23035",
        "name": "Forced door"
      },
      "priority": 8,
      "state": "unacknowledged",
      "active": false,
      "division": {
        "href": "https://localhost:8904/api/divisions/2"
      },
      "event": {
        "href": "https://localhost:8904/api/events/10135"
      },
      "notePresets": [
        "False alarm confirmed by surveillance",
        "Security staff dispatched"
      ],
      "view": {
        "href": "https://localhost:8904/api/alarms/92210/view"
      },
      "comment": {
        "href": "https://localhost:8904/api/alarms/92210/comment"
      },
      "acknowledge": {
        "href": "https://localhost:8904/api/alarms/92210/acknowledge"
      },
      "acknowledgeWithComment": {
        "href": "https://localhost:8904/api/alarms/92210/acknowledge"
      },
      "process": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      },
      "processWithComment": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      },
      "forceProcess": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      }
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/alarms/next?start=92143&pos=61320"
  },
  "updates": {
    "href": "https://localhost:8904/api/alarms/updates?id=92143.1"
  }
}
Alarm updates: object
updates:  Alarm summary 
An array of summaries of alarms created or modified since the previous call.

Alarm summary
next: object
Follow this link to perform another long poll.

href: string
Example
{
  "updates": [
    {
      "href": "https://localhost:8904/api/alarms/10135",
      "id": "10135",
      "time": "2016-02-18T19:21:52Z",
      "message": "External bulk loading bay door has been forced",
      "source": {
        "id": "1321",
        "name": "External bulk loading bay door",
        "href": "https://localhost:8904/api/doors/1321"
      },
      "type": "Forced door",
      "eventType": {
        "id": "23035",
        "name": "Forced door"
      },
      "priority": 8,
      "state": "unacknowledged",
      "active": false,
      "division": {
        "href": "https://localhost:8904/api/divisions/2"
      },
      "event": {
        "href": "https://localhost:8904/api/events/10135"
      },
      "notePresets": [
        "False alarm confirmed by surveillance",
        "Security staff dispatched"
      ],
      "view": {
        "href": "https://localhost:8904/api/alarms/92210/view"
      },
      "comment": {
        "href": "https://localhost:8904/api/alarms/92210/comment"
      },
      "acknowledge": {
        "href": "https://localhost:8904/api/alarms/92210/acknowledge"
      },
      "acknowledgeWithComment": {
        "href": "https://localhost:8904/api/alarms/92210/acknowledge"
      },
      "process": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      },
      "processWithComment": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      },
      "forceProcess": {
        "href": "https://localhost:8904/api/alarms/92210/process"
      }
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/alarms/updates?id=10135"
  }
}
Alarm summary: object
/api/alarms returns an array of these, and /api/alarms/{id} returns one with more fields.

href: string (url)
A link to the details of this alarm.

id: string
An alphanumeric identifier for this alarm, unique to the server.

time: string
The time the alarm occurred.

message: string
The alarm's message.

source: object
The ID and href are new to 8.10.

id: string
The alphanumeric ID of the alarm's source item.

name: string
The name of the alarm's source item at the time of the event.

href: string (url)
Link to the source item.

type: string
The name of this alarm's event type.

eventType: object
ID and name of the event's or alarm's type. There is a long list of them at /events/groups.

Unlike the type field, this has the same format in an event as it does in an alarm.

Added in 8.90. Because it is a new field, it does not appear by default. Ask for it using the fields parameter.

id: string
The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with /events.

name: string
The name of the event type.

priority: integer 0 ≤ x ≤ 9
Numeric priority. 9 is critical and 0 is not an event.

state: string  unacknowledged, acknowledged, processed 
The state of the alarm. Alarms start at "unacknowledged".

active: boolean
Clients cannot process active alarms.

division: object
The division entity representing the division of the alarm. GET the href for full details.

href: string (url)
event: object
A block containing a link ("href") to the details page of this alarm's event. Useful if you want an alarm's related items.

This mirrors an event's alarm field, which comes the other way.

Added in 8.90. Not returned by default - you need to ask for it using the fields query parameter.

href: string (url)
Link to the event page for this alarm. GET it for the fields found on an event that are not on an alarm.

notePresets: string[]
Preset alarm notes to use for this specific alarm. Missing if the alarm does not have specific notes; in this case the client should fall back to the server defaults.

string
view: object
POST an alarm update request JSON object to the href to indicate the operator has viewed the alarm.

href: string (url)
comment: object
POST an alarm update request JSON object to the href to place an arbitrary alarm note against the alarm.

href: string (url)
acknowledge: object
POST an alarm update request JSON object to the href to acknowledge an alarm. Missing if the system has mandatory alarm notes on for this alarm, or if the alarm is already acknowledged.

href: string (url)
acknowledgeWithComment: object
POST an alarm update request JSON object to the href to acknowledge an alarm and place an alarm note against it. Missing if the alarm is already acknowledged.

href: string (url)
process: object
POST an alarm update request JSON object to the href to process an alarm. Missing if the system has mandatory alarm notes on for this alarm. Missing if the alarm cannot be processed (if it is active, for example).

href: string (url)
processWithComment: object
POST an alarm update request JSON object to the href to process an alarm and place an alarm note against it. Missing if the alarm cannot be processed (if it is active, for example).

href: string (url)
forceProcess: object
POST an alarm update request JSON object to this href to process an active alarm. Missing if the alarm is not active (in which case you will have process and processWithComment links instead).

This action can take a comment in the body. In fact an explanation is recommended since force-processing an alarm is unusual.

href: string (url)
Example
{
  "href": "https://localhost:8904/api/alarms/10135",
  "id": "10135",
  "time": "2016-02-18T19:21:52Z",
  "message": "External bulk loading bay door has been forced",
  "source": {
    "id": "1321",
    "name": "External bulk loading bay door",
    "href": "https://localhost:8904/api/doors/1321"
  },
  "type": "Forced door",
  "eventType": {
    "id": "23035",
    "name": "Forced door"
  },
  "priority": 8,
  "state": "unacknowledged",
  "active": false,
  "division": {
    "href": "https://localhost:8904/api/divisions/2"
  },
  "event": {
    "href": "https://localhost:8904/api/events/10135"
  },
  "notePresets": [
    "False alarm confirmed by surveillance",
    "Security staff dispatched"
  ],
  "view": {
    "href": "https://localhost:8904/api/alarms/92210/view"
  },
  "comment": {
    "href": "https://localhost:8904/api/alarms/92210/comment"
  },
  "acknowledge": {
    "href": "https://localhost:8904/api/alarms/92210/acknowledge"
  },
  "acknowledgeWithComment": {
    "href": "https://localhost:8904/api/alarms/92210/acknowledge"
  },
  "process": {
    "href": "https://localhost:8904/api/alarms/92210/process"
  },
  "processWithComment": {
    "href": "https://localhost:8904/api/alarms/92210/process"
  },
  "forceProcess": {
    "href": "https://localhost:8904/api/alarms/92210/process"
  }
}
Alarm detail:
/api/alarms/{id} returns one of these. It contains everything from the alarm summary results, plus some extra fields that are too expensive to compute and return for large result sets.

Alarm summary
details: string
The full alarm details text. This may be up to 2048 UTF-8 characters, each of which could (theoretically) be four bytes long.

history:  Alarm history entry 
An array of alarm history entries. Missing if there is no history.

Alarm history entry
instruction: object
GET the href it contains to retrieve the alarm instruction body inside an HTML document.

In 9.10 and later you can request the instruction's name using the query parameter fields=instruction.name. If you want the href as well, add ,instruction.href.

Missing if there is no alarm instruction for this alarm.

href: string (url)
name: string
cardholder: object
The cardholder entity associated with this alarm. GET the href for full details. Missing if this alarm does not have an associated cardholder.

href: string (url)
name: string
In versions up to and including 8.10 this is the current name of the cardholder. In 8.20 it is the name of the cardholder at the time of the event.

firstName: string
The current value of the firstName field of this cardholder. Added in 8.20.

lastName: string
The current value of the lastName field of this cardholder. Added in 8.20.

Example
{
  "href": "https://localhost:8904/api/alarms/10135",
  "id": "10135",
  "time": "2016-02-18T19:21:52Z",
  "message": "External bulk loading bay door has been forced",
  "source": {
    "id": "1321",
    "name": "External bulk loading bay door",
    "href": "https://localhost:8904/api/doors/1321"
  },
  "type": "Forced door",
  "eventType": {
    "id": "23035",
    "name": "Forced door"
  },
  "priority": 8,
  "state": "unacknowledged",
  "active": false,
  "division": {
    "href": "https://localhost:8904/api/divisions/2"
  },
  "event": {
    "href": "https://localhost:8904/api/events/10135"
  },
  "notePresets": [
    "False alarm confirmed by surveillance",
    "Security staff dispatched"
  ],
  "view": {
    "href": "https://localhost:8904/api/alarms/92210/view"
  },
  "comment": {
    "href": "https://localhost:8904/api/alarms/92210/comment"
  },
  "acknowledge": {
    "href": "https://localhost:8904/api/alarms/92210/acknowledge"
  },
  "acknowledgeWithComment": {
    "href": "https://localhost:8904/api/alarms/92210/acknowledge"
  },
  "process": {
    "href": "https://localhost:8904/api/alarms/92210/process"
  },
  "processWithComment": {
    "href": "https://localhost:8904/api/alarms/92210/process"
  },
  "forceProcess": {
    "href": "https://localhost:8904/api/alarms/92210/process"
  },
  "details": "Forced door",
  "history": [
    {
      "time": "2016-02-18T19:21:52Z",
      "action": "viewed",
      "comment": "Operator viewed alarm properties",
      "operator": {
        "name": "System Operator"
      }
    }
  ],
  "instruction": {
    "href": "https://localhost:8904/api/alarms/92210/instructions",
    "name": "Forced door instruction"
  },
  "cardholder": {
    "href": "https://localhost:8904/api/cardholders/325",
    "name": "Smith, Jane",
    "firstName": "Jane",
    "lastName": "Smith-Jones"
  }
}
Alarm history entry: object
time: string
The time the history entry was added.

action: string  legacy, comment, acknowledge, process, acknowledgeActive, escalated, viewed 
The type of the history entry.

comment: string
The added comment, or a textual description of some occurrence related to the alarm.

operator: object
The operator that created the history event.

Example
{
  "time": "2016-02-18T19:21:52Z",
  "action": "viewed",
  "comment": "Operator viewed alarm properties",
  "operator": {
    "name": "System Operator"
  }
}
Alarm update request: object
comment: string
Optional for some methods that update alarms. Contains a comment placed by the operator.

Example
{
  "comment": "Alarm was adequately explained."
}
Event search: object
/api/events and /api/events/updates return this structure.

events:  Event summary 
A list of event summaries.

Event summary
previous: object
Follow this link to make a non-blocking call to collect the previous page of events (in order of arrival). It will return an empty page if you have already received the first event in the database.

href: string (url)
next: object
Follow this link to make a non-blocking call to collect the next page of events, moving forward in arrival time. It will return an empty page if no more events are available.

href: string (url)
updates: object
This is a link to an updates call. Follow it to make a blocking call to collect more events. If there are none, the call will block until one arrives or the call times out. This link will contain your column select, pagination, and filtering parameters (fields, top, after, before, source, type, etc.) but it drops deadline. You need to add that to each call.

href: string (url)
Example
{
  "events": [
    {
      "href": "https://localhost:8904/api/events/61320",
      "id": "61320",
      "serverDisplayName": "ruatoria.satellite.int",
      "time": "2016-02-18T19:21:52Z",
      "message": "Operator logon failed for FT Workstation on GNZ-PC1439",
      "occurrences": 2,
      "priority": 3,
      "alarm": {
        "state": "unacknowledged",
        "href": "https://localhost:8904/api/alarms/61320"
      },
      "operator": {
        "href": "https://localhost:8904/api/cardholders/325",
        "name": "Chong, Marc"
      },
      "source": {
        "id": "321",
        "name": "FT Workstation on GNZ-PC1439",
        "href": "https://localhost:8904/api/items/321"
      },
      "group": {
        "id": "35",
        "name": "Invalid Logon"
      },
      "type": {
        "id": "601",
        "name": "Operator logon failed"
      },
      "eventType": {
        "id": "601",
        "name": "Operator logon failed"
      },
      "division": {
        "id": "2",
        "href": "https://localhost:8904/api/divisions/2",
        "name": "Root division"
      },
      "cardholder": {
        "href": "https://localhost:8904/api/cardholders/325",
        "id": "325",
        "name": "Bruce, Jennifer",
        "firstName": "Jennifer",
        "lastName": "Caitlin"
      },
      "entryAccessZone": {
        "href": "https://localhost:8904/api/access_zones/333",
        "name": "Brookwood showroom",
        "id": "333"
      },
      "exitAccessZone": {
        "href": "https://localhost:8904/api/access_zones/913",
        "name": "Compressor room",
        "id": "913"
      },
      "door": {
        "href": "https://localhost:8904/api/doors/745",
        "name": "Main hoist door"
      },
      "accessGroup": {
        "href": "https://localhost:8904/api/access_groups/352"
      },
      "card": {
        "facilityCode": "A12345",
        "number": "78745",
        "issueLevel": 1
      },
      "modifiedItem": {
        "href": "https://localhost:8904/api/cardholders/325",
        "type": {
          "id": "1",
          "name": "Cardholder"
        }
      },
      "next": {
        "href": "https://localhost:8904/api/events?pos=61320"
      },
      "previous": {
        "href": "https://localhost:8904/api/events?pos=61320&previous=True"
      },
      "updates": {
        "href": "https://localhost:8904/api/events/updates?pos=61320"
      }
    }
  ],
  "previous": {
    "href": "https://localhost:8904/api/events/next?previous=True&pos=61320"
  },
  "next": {
    "href": "https://localhost:8904/api/events/next?pos=61320"
  },
  "updates": {
    "href": "https://localhost:8904/api/events/updates?pos=61320"
  }
}
Event summary: object
/api/events and /api/events/updates return an array of these, and /api/events/{id} returns one with more fields.

The message and event type in this example indicate an operator attempting to log in with an incorrect password, but for the sake of illustration the example also contains references to items that would never appear on such an event. The card, cardholder, and access zones, for example.

href: string (url)
A link to this event's details.

id: string
An alphanumeric identifier for this event, unique to the server.

serverDisplayName: string
The host name of this event's origin server, if it was aggregated from a remote host. Absent for local events.

Note that this is not the descriptive name of the remote server's item, but the host name used for address resolution.

New to 8.40.

time: string (date-time)
The time the event occurred.

message: string
The event's message.

occurrences: integer x ≥ 2
If an event arrives with the same essential properties as a previous event, the server will start counting them. Each event is still individually addressable and will appear in the API as normal, but the first in the group will also have this property. It only appears on the first, and it does not appear if the event is a singleton.

If present on an event's detail or alarm page, there will also be a lastOccurrenceTime.

Outside of the alarm block, which contains an alarm's state, this is the only field on an event that can change.

priority: integer 0 ≤ x ≤ 9
Numeric priority. 9 is critical and 0 is not an event.

alarm: object
If an event is also an alarm, this object will contain its state and a link to its details page in the alarms controller.

Only the first event in a group (see the occurrences field) can become an alarm.

state: string  unacknowledged, acknowledged, processed 
Alarms start unacknowledged. Acknowledging or processing them changes that.

href: string (url)
Link to the alarm entity corresponding to the event. GET the href for full alarm details.

operator: object
The href and name of the operator behind this event. This will appear when an operator has modified an item. New in v8.00.

The item he or she modified will appear in the modifiedItem block (added in 8.40).

href: string (url)
Link to the operator who caused this event by editing an item. New in 8.00.

name: string
The name the operator held at the time. New in 8.40.

source: object
ID and name of the source of the event, as recorded at the time of the event.

id: string
The alphanumeric ID of the event source item. Search for events with the same source as this one with source=321 in the query parameters.

name: string
This could be different from the current name of the source item.

href: string (url)
Link to the source item. New in 8.00.

group: object
ID and name of the event group this event belongs to. Do not confuse this with an access group or operator group: this is the event type group to which the event's type belongs. There are about 150 and you can list them at /events/groups.

id: string
The alphanumeric ID of the event group. Search for events of the same rough category as this one with type=35 in the query parameters.

name: string
The name of the event group.

type: object
ID and name of the event's type. There is a long list of them at /events/groups.

id: string
The alphanumeric ID of the event type. To search for events of the same type as this example, put type=23035 in the query parameters of /events.

name: string
The name of the event type.

eventType: object
ID and name of the event's or alarm's type. There is a long list of them at /events/groups.

Unlike the type field, this has the same format in an event as it does in an alarm.

Added in 8.90. Because it is a new field, it does not appear by default. Ask for it using the fields parameter.

id: string
The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with /events.

name: string
The name of the event type.

division: object
ID, name, and href of the event's division (which is the division of the event's source item, for most event types).

id: string
The alphanumeric ID of the event's division. Search for other events from items in the same division as this example by putting division=2 in the query parameters.

href: string (url)
The link to the division item.

name: string
The division's name. Added in 8.40.

cardholder: object
Summary information about an event's cardholder, if there is one. This will be the cardholder who badged their card at a door in an access event, or the cardholder an operator modified in an operator event. Search for other events related to this example's cardholder with cardholder=325 in the query parameters.

href: string (url)
Link to the cardholder entity representing the cardholder of the event. GET the href for full details.

id: string
The alphanumeric ID of the cardholder associated with this event.

name: string
In versions up to and including 8.10 this is the current name of the cardholder. In 8.20 it is the name of the cardholder at the time of the event.

firstName: string
The current value of the firstName field of this cardholder. Added in 8.20.

lastName: string
The current value of the lastName field of this cardholder. Added in 8.20.

entryAccessZone: object
The name and href of the entry access zone related to the event. In the case of card events, it is the zone into which a cardholder was attempting to gain access.

That is true for successful entries, successful exits, and access denials. Regardless of whether the cardholder badged at the entry or exit reader, this field refers to the zone that he or she attempted to access.

For example, for 'Card entry granted' events this field will contain the door's entry zone, but for 'Card exit granted' events this field will contain the door's exit zone.

href: string (url)
Link to the access zone entity representing the entry access zone related to this event. GET the href for the access zone's full details. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').

name: string
The current name of the access zone. Expect a future version of Command Centre to change this to change to the name of the access zone at the time of the event.

id: string
Deprecated.

exitAccessZone: object
The name and href of the exit access zone related to the event. In card events, it is the zone from which a cardholder was attempting to leave, if the door had an exit zone configured (many do not).

That is true for successful entries, successful exits, and access denials. Regardless of whether the cardholder badged at the entry or exit reader, this field refers to the zone that he or she attempted to leave.

For example, for 'Card entry granted' events this field will contain the door's exit zone, if there was one, but for 'Card exit granted' events this field will contain the door's entry zone.

href: string (url)
Link to the access zone entity representing the exit access zone related to this event. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').

name: string
The current name of the exit access zone. Expect a future version of Command Centre to change this to change to the name of the exit access zone at the time of the event.

id: string
Deprecated.

door: object
The name and href of the door related to the event. These are not as common as you may think, because when a door is relevant (to card events, for example) it is usually the event's source. New in 8.10.

href: string (url)
Link to the entity representing the door related to this event. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').

name: string
The name the door had when the event occurred.

accessGroup: object
The href of the access group that a cardholder just gained or lost in a 'Membership Activated' or 'Membership Expired' event. Those happen when a group membership's 'from' or 'until' time passes.

DEPRECATED for the events generated when an operator creates, modifies, or deletes an access group. Use the 'modifiedItem' field instead (new in 8.40).

href: string (url)
Link to the access group related to this event.

card: object
Details of the card associated with the event.

Versions prior to 8.60 returned this block and a card number of zero for all access events, even if they did not involve a credential (after a person entered their user code at a keypad, for example). Version 8.60 does not return this block for such events.

facilityCode: string
The card's facility code at the time of the event expressed as one letter followed by up to five digits.

number: string
The card's number at the time of the event. Despite the name it may not necessarily be an actual number; mobile card numbers are arbitrary strings, for example.

Note that card numbers are not guaranteed unique. The combination of facility code, issue level, and card number will be unique for card types that have a facility code and issue level.

Before 8.60 this number was a signed 32-bit integer, so numeric card numbers greater than 2^31 came out negative and mobile card numbers were arbitrary. In 8.60 numeric card numbers are positive and mobile card numbers are their ID strings, which operators can set to anything, and default to GUIDs.

issueLevel: integer
The issue level of the card at the time.

modifiedItem: object
The href and type of the item that an operator created, changed, or deleted, for those kinds of events.

New in 8.40.

href: string (url)
Link to the item that this event modified. Watch for 404s: this link will be here even for deleted items.

type: object
id: string
A short alphanum identifying the item's type.

name: string
A human-readable name of the item's type, suitable for display.

This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.

next: object
The URL to the search that will return the page of events following this one. It will include your search filters and pagination parameters.

This link is intended for integrations that take great gulps of events and send them to a downstream system. If it suffers a problem in the middle of a result set, it needs to record where in the event trail it got up to so that when it restarts it will not miss or duplicate events.

The next link on the last event in a result set will be the same as the next link outside the results.

New to 8.70.

href: string (url)
previous: object
The URL to the search that will return the page of events preceding this one. It will include your search filters and pagination parameters.

The previous link on the first event in a result set will be the same as the previous link outside the results, since they both indicate the latest event that preceded the result set.

New to 8.70.

href: string (url)
updates: object
The URL to the updates call that will return the page of events following this one, or wait for one to arrive if there are none. It will include your search filters and pagination parameters.

The updates link on the last event in a result set will be the same as the updates link outside the results, since they both indicate the event that will be at the top of the next result set.

New to 8.70.

href: string (url)
Example
{
  "href": "https://localhost:8904/api/events/61320",
  "id": "61320",
  "serverDisplayName": "ruatoria.satellite.int",
  "time": "2016-02-18T19:21:52Z",
  "message": "Operator logon failed for FT Workstation on GNZ-PC1439",
  "occurrences": 2,
  "priority": 3,
  "alarm": {
    "state": "unacknowledged",
    "href": "https://localhost:8904/api/alarms/61320"
  },
  "operator": {
    "href": "https://localhost:8904/api/cardholders/325",
    "name": "Chong, Marc"
  },
  "source": {
    "id": "321",
    "name": "FT Workstation on GNZ-PC1439",
    "href": "https://localhost:8904/api/items/321"
  },
  "group": {
    "id": "35",
    "name": "Invalid Logon"
  },
  "type": {
    "id": "601",
    "name": "Operator logon failed"
  },
  "eventType": {
    "id": "601",
    "name": "Operator logon failed"
  },
  "division": {
    "id": "2",
    "href": "https://localhost:8904/api/divisions/2",
    "name": "Root division"
  },
  "cardholder": {
    "href": "https://localhost:8904/api/cardholders/325",
    "id": "325",
    "name": "Bruce, Jennifer",
    "firstName": "Jennifer",
    "lastName": "Caitlin"
  },
  "entryAccessZone": {
    "href": "https://localhost:8904/api/access_zones/333",
    "name": "Brookwood showroom",
    "id": "333"
  },
  "exitAccessZone": {
    "href": "https://localhost:8904/api/access_zones/913",
    "name": "Compressor room",
    "id": "913"
  },
  "door": {
    "href": "https://localhost:8904/api/doors/745",
    "name": "Main hoist door"
  },
  "accessGroup": {
    "href": "https://localhost:8904/api/access_groups/352"
  },
  "card": {
    "facilityCode": "A12345",
    "number": "78745",
    "issueLevel": 1
  },
  "modifiedItem": {
    "href": "https://localhost:8904/api/cardholders/325",
    "type": {
      "id": "1",
      "name": "Cardholder"
    }
  },
  "next": {
    "href": "https://localhost:8904/api/events?pos=61320"
  },
  "previous": {
    "href": "https://localhost:8904/api/events?pos=61320&previous=True"
  },
  "updates": {
    "href": "https://localhost:8904/api/events/updates?pos=61320"
  }
}
Event detail:
/api/events/{id} returns one of these. It contains everything from the event summary results, plus some extra fields that are too expensive to compute and return for large result sets.

Like the example in the summary, this example is forced: no actual Command Centre event will contain all of these fields.

Event summary
lastOccurrenceTime: string (date-time)
When the event has occurred multiple times due to flooding, this will show the time it occurred most recently. It only appears with occurrences, and you will only see it on the first event in a group.

details: string
The full alarm details text. This may be up to 2048 UTF-8 characters, each of which could (theoretically) be four bytes long.

location: object
Added in 9.00, this block gathers together information present in about 80 different event types that contain a cardholder's location, presenting it in a consistent way.

It allows a client that is interested in movements to extract what it needs without needing to know what event types to look for or how they represent these three fields:

The cardholder whose location the event contains,
the access zone that they started in before the event, and
the item that indicates their location after the event.
The location block will only be in a result if you ask for it using the fields query parameter.

type: string  moved, observed, denied 
This enum divides location events into three types:

moved means a cardholder moved through a door from one zone to another. Whether an operator moved them using our software, or they did it themselves using a plastic card, a mobile credential, biometrics, a QR code, a vehicle plate, or something implemented by a third-party integration, provided they were granted access, this field's value will be moved.

observed means they did something that indicated their location, such as logging in to a workstation or an alarms terminal, but they did not move.

denied means the cardholder authenticated successfully and requested access at a door but failed the access check. It also appears on some event types where the cardholder did not successfully authenticate; for those events there will be no cardholder block, covered next.

cardholder: object
A block describing the cardholder whose location is recorded by the event.

It will be missing if the cardholder did not successfully authenticate: perhaps they used their card but did not complete a second factor. In most cases, though, if a cardholder does not authenticate properly (gets their PIN wrong, for example) there will be no location block at all.

name: string
The cardholder's name at the time of the event.

href: string (url)
The cardholder item's href. This will only be present if the cardholder has not been deleted and your operator has the right to view it.

beforeLocation: object
The href and name of the access zone or reception that the cardholder was in before this event occurred. Future versions of Command Centre may add more item types as "before locations" so check its canonical type name, covered in the afterLocation description below.

The before location is only useful (and present) for 'moved'-type events, which happen when a cardholder changes location. For 'observed' and 'denied'-type events, which happen when a cardholder does not move, afterLocation shows where they were.

The name was correct at the time of the event. It may be missing.

The href will be missing if your operator does not have the right to view the location. That also happens when it is deleted.

If the cardholder moved from outside there will be a field in this block called outside with the value true, and canonicaltypeName and href will be missing.

afterLocation: object
The href and name of the item that indicates the cardholder's location after the event. For 'observed' and 'denied'-type events, this is also where they started.

For 'moved' and 'denied' events in 9.00 it will be an access zone or reception.

For 'observed' events it could be a workstation or an alarms terminal at which the person authenticated.

Future versions of Command Centre may add more item types. So that you can branch on them, this block and beforeLocation contain a field called canonicalTypeName. The values it returns are fixed: it is safe to make decisions based on the value. We will add new values as Command Centre grows, but after its first appearance an item type's canonicalTypeName will not change.

Common canonical type names are accesszone, reception, workstation, and (for a T20 alarms terminal that uses the H-Bus protocol) hbusterminal. You can get the full list with the item types.

If they moved "outside the system" (entering through a door with no entry zone, or exiting through a door with no exit zone), there will be no href or canonical type, but there will be a field called outside with the value true.

The href will also be missing if your operator does not have the right to view their new location.

If present, the item's name was correct at the time of the event. It may have changed since. It will be missing for some event types such as 15583, which happens when an operator moves a cardholder outside the system using an operational client.

Example
{
  "href": "https://localhost:8904/api/events/61320",
  "id": "61320",
  "serverDisplayName": "ruatoria.satellite.int",
  "time": "2016-02-18T19:21:52Z",
  "message": "Operator logon failed for FT Workstation on GNZ-PC1439",
  "occurrences": 2,
  "priority": 3,
  "alarm": {
    "state": "unacknowledged",
    "href": "https://localhost:8904/api/alarms/61320"
  },
  "operator": {
    "href": "https://localhost:8904/api/cardholders/325",
    "name": "Chong, Marc"
  },
  "source": {
    "id": "321",
    "name": "FT Workstation on GNZ-PC1439",
    "href": "https://localhost:8904/api/items/321"
  },
  "group": {
    "id": "35",
    "name": "Invalid Logon"
  },
  "type": {
    "id": "601",
    "name": "Operator logon failed"
  },
  "eventType": {
    "id": "601",
    "name": "Operator logon failed"
  },
  "division": {
    "id": "2",
    "href": "https://localhost:8904/api/divisions/2",
    "name": "Root division"
  },
  "cardholder": {
    "href": "https://localhost:8904/api/cardholders/325",
    "id": "325",
    "name": "Bruce, Jennifer",
    "firstName": "Jennifer",
    "lastName": "Caitlin"
  },
  "entryAccessZone": {
    "href": "https://localhost:8904/api/access_zones/333",
    "name": "Brookwood showroom",
    "id": "333"
  },
  "exitAccessZone": {
    "href": "https://localhost:8904/api/access_zones/913",
    "name": "Compressor room",
    "id": "913"
  },
  "door": {
    "href": "https://localhost:8904/api/doors/745",
    "name": "Main hoist door"
  },
  "accessGroup": {
    "href": "https://localhost:8904/api/access_groups/352"
  },
  "card": {
    "facilityCode": "A12345",
    "number": "78745",
    "issueLevel": 1
  },
  "modifiedItem": {
    "href": "https://localhost:8904/api/cardholders/325",
    "type": {
      "id": "1",
      "name": "Cardholder"
    }
  },
  "next": {
    "href": "https://localhost:8904/api/events?pos=61320"
  },
  "previous": {
    "href": "https://localhost:8904/api/events?pos=61320&previous=True"
  },
  "updates": {
    "href": "https://localhost:8904/api/events/updates?pos=61320"
  },
  "lastOccurrenceTime": "2016-02-18T19:21:59Z",
  "details": "Originating IP address: 192.168.2.3",
  "location": {
    "type": "moved",
    "cardholder": {
      "name": "Jackson",
      "href": "https://localhost:8904/cardholders/325"
    },
    "beforeLocation": {
      "href": "https://localhost:8904/api/access_zones/333",
      "name": "Lvl 1 lift lobby",
      "canonicalTypeName": "accesszone"
    },
    "afterLocation": {
      "outside": true
    }
  }
}
Event POST body:
type: object 
A new event must contain either this or (if your server is running 8.90 or later) eventType. Without one of them, the POST will fail.

The event type is mandatory because the server cannot assume a reasonable default. It can for the other fields.

If you send both a type block and an eventType block to a server running 8.90 or later it will use eventType.

href: string (url)
Take this href from the list of event types. Note that you can only use event types in one of the thirty external event groups with IDs 57-66 and 190-209. Command Centre ships with one event type per group, IDs 4000-4009 and 6010-6029, but you can create 970 more using the External Event Type Configuration utility.

eventType: object 
Synonym for type, introduced in 8.90.

href: string (url)
source: object
This block should contain the href of the item you wish to use as the source of your event. It can be any site item to which your operator has view access including all hardware, access zones, fence zones, doors, lockers, car parks, servers, external systems, and many other item types. If you do not supply one Command Centre will use the REST Client item identified by the API key in the Authorization header.

Cardholders cannot be event sources. To relate a cardholder to your event, use the cardholder block.

Make sure that your operator has the 'Create Events and Alarms' privilege on this item's division. 8.90 and later insist on it.

The API will use the source's division as the event's division.

href: string (url)
Get the href using the API controller for that item type (such as doors) or from /items.

priority: integer 1 ≤ x ≤ 9
It is not possible to submit an event with priority zero in the body, but if you submit an event with no priority it will use the one on the event type's action plan, which can be zero.

time: string (date-time)
Like all other fields in this POST apart from the type, this field is optional. If you send it, it must be in the format described here.

If you do not send this, the server will use the time that it received your request (its "now").

message: string
This is the first thing an operator will see when they look at this event. Some interactive clients do not give it a lot of room on screen so put the important parts of your message first. It has a limit of 1024 characters.

details: string
Command Centre will attach this string to event, as it does the message, but operators will have to look more closely at the event to see it. On the upside, it can be longer than the message: 2048 characters in 8.10.

cardholder: object
If you wish to attach a cardholder to your event, link it here. Reports can show or filter by the cardholder.

href: string (url)
This can be the href of any cardholder to which your operator has view access. Get the href from the cardholders controller.

operator: object
If you wish to attach an operator to your event, link it here. Like the cardholder, reports can show or filter by the operator.

href: string (url)
This can be the href of any cardholder to which your operator has view access. It does not need to be an operator (a member of an operator group).

entryAccessZone: object
If you wish to attach an access zone to your event, link it here. Reports can filter by and show the entry access zone on events.

href: string (url)
This can be the href of any access zone to which your operator has view access. Get the href using the access zones controller or from /items.

accessGroup: object
If you wish to attach an access group to your event, link it here.

Unlike cardholders, operators, and entry access zones, access groups do not appear in Command Centre activity reports. You can add a filter to restrict an activity report by access groups, but the group that allowed an event into the report will not appear in a column.

Like all the other items you link to your event it will, of course, appear when you GET the event from the API later.

href: string (url)
This can be the href of any access group to which your operator has view access. Get the href using the groups or items controller.

lockerBank: object
If you wish to attach a locker bank to your event, link it here.

Like an event's access group, you can filter a Command Centre activity report to events that involve a locker bank, but the bank will not appear in the report itself.

If you link both a locker and a locker bank to an event, Command Centre does not require that the locker is in the locker bank, but you may find that downstream reporting software misbehaves when it is not.

href: string (url)
This can be the href of any locker bank to which your operator has view access. Get the href using the locker banks or items controller.

locker: object
If you wish to link a locker to your event, do it here. Like an event's access group and locker bank, you can filter a Command Centre activity report to events that involve a locker, but the locker will not appear in the report itself.

href: string (url)
This can be the href of any locker your operator can view. Get the href from the items controller or the lockers field of a locker bank.

door: object
If you wish to link a door to your event for later extraction or a report filter, do it here.

href: string (url)
This can be the href of any door your operator can see. Get the href using the doors or items controller.

Example
{
  "type": {
    "href": "https://localhost:8904/api/events/types/4000"
  },
  "eventType": {
    "href": "https://localhost:8904/api/events/types/4000"
  },
  "source": {
    "href": "https://localhost:8904/api/doors/745"
  },
  "priority": 2,
  "time": "2019-02-21T14:55:00Z",
  "message": "Glass break detected in southwest sauna",
  "details": "",
  "cardholder": {
    "href": "https://localhost:8904/api/cardholders/325"
  },
  "operator": {
    "href": "https://localhost:8904/api/cardholders/5398"
  },
  "entryAccessZone": {
    "href": "https://localhost:8904/api/access_zones/333"
  },
  "accessGroup": {
    "href": "https://localhost:8904/api/access_groups/352"
  },
  "lockerBank": {
    "href": "https://localhost:8904/api/locker_banks/4566"
  },
  "locker": {
    "href": "https://localhost:8904/api/lockers/3456"
  },
  "door": {
    "href": "https://localhost:8904/api/doors/745"
  }
}
Event POST example:
This is an example showing how you could create a new event.

See the POST for where to get the address of the endpoint, the rules around event types and items, and what happens to your event after you create it, and see the schema definition for a description of each field.

Only eventType is required for the API to accept the POST, but an event is not much without a source item and a message string, and it never hurts to add some details.

To make this example work on a server running 8.80 or earlier change eventType to type.

Example
{
  "eventType": {
    "href": "https://localhost:8904/api/events/types/4000"
  },
  "source": {
    "href": "https://localhost:8904/api/doors/745"
  },
  "message": "Glass break detected in southwest sauna",
  "details": "Temperature dropping"
}
Event groups: object
Calls to /api/events/groups/ return this object, which is a named array of groups of event types.

eventGroups: object[]
An array of event group objects. There will be about 150. Most groups contain fewer than 100 event types; one contains around 200.

object
id: string
The alphanumeric ID of the event group. Use this ID in the group filter when requesting events.

name: string
The name of the event group.

eventTypes: object[]
An array of all the event types in the group.

object
id: string
The alphanumeric ID of the event type. Use this ID in the type filter when requesting events.

name: string
Example
{
  "eventGroups": [
    {
      "id": 35,
      "name": "Invalid Logon",
      "eventTypes": [
        {
          "href": "https://localhost:8904/api/events/types/601",
          "id": "601",
          "name": "Operator logon failed"
        },
        {
          "href": "https://localhost:8904/api/events/types/20065",
          "id": "20065",
          "name": "Terminal: Invalid User Code"
        },
        {
          "href": "https://localhost:8904/api/events/types/23052",
          "id": "23052",
          "name": "Wrong Code only Code"
        }
      ]
    }
  ]
}
Divisions: object
Calls inside /api/divisions/ return this object, which is simply a named array of objects each containing some information about a division.

results:  Division 
An array of division objects.

Division
next: object
A site generally does not have too many divisions. You should be able to collect them all in one request by using the top parameter. If not, follow this link to get the next page of divisions.

href: string (url)
Example
{
  "results": [
    {
      "href": "https://localhost:8904/divisions/2",
      "id": "2",
      "name": "Root division",
      "description": "Contains all other divisions",
      "serverDisplayName": "ruatoria.satellite.int",
      "parent": {
        "href": "https://localhost:8904/divisions/2"
      },
      "visitorManagement": {
        "active": true,
        "visitortypes": [
          {
            "href": "https://localhost:8904/api/divisions/2/visitor_types/925",
            "accessGroup": {
              "name": "Visitor access group 1",
              "href": "https://localhost:8904/api/access_groups/925"
            },
            "hostAccessGroups": [
              {
                "accessGroup": {
                  "name": "Host access group 1",
                  "href": "https://localhost:8904/api/access_groups/938"
                }
              }
            ],
            "visitorAccessGroups": [
              {
                "accessGroup": {
                  "name": "Access group 22",
                  "href": "https://localhost:8904/api/access_groups/926"
                }
              },
              {
                "accessGroup": {
                  "name": "Access group 30",
                  "href": "https://localhost:8904/api/access_groups/927"
                }
              }
            ]
          }
        ]
      }
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/divisions/view_events?skip=10"
  }
}
Division: object
When a REST call returns the division of a Command Centre item such as a cardholder or access group, or when you ask it for the divisions in which an operator has a particular privilege, it will give an href inside /divisions/. Following that href will return one of these.

href: string (url)
A self reference.

id: string
The alphanumeric ID of the division. Use this ID in the division filter when requesting events.

name: string
The division's name.

description: string
The division's description. New in 8.50. Not sent by default; ask for it with fields.

serverDisplayName: string
If you are running a multi-server installation and this item is homed on a remote server, this field will contain the name of that server. This field is missing from items that are held on the machine that served the API request. Added in 8.40.

This is a read-only field. The server will ignore it if you send it.

parent: object
An object containing an href to the division entity representing the current division's parent. GET the href for full details.

href: string (url)
visitorManagement: object
An object containing the division's visitor management configuration. It only appears if you ask for it with fields=visitorManagement and if your operator has the necessary privilege ('View Site', 'Edit Site', 'View Visits', 'Edit Visits', or 'Manage Receptions').

active: boolean
If present and true, this division has its own visitor management configuration. Otherwise it uses its parent's. That is also indicated by the presence or absence of the visitorTypes block.

visitortypes: object[]
This is an array of items, each containing a 'visitor type'. A visitor type comprises three things: an access group, to which visitor management will add every visitor as soon as they are on the visit, host access groups, to one of which the host cardholder must belong, and visitor access groups, to which Command Centre will add a visitor when they sign in.

When creating a visit you must pick a visitor type that is in the same division as the visit's reception. The visitor type you pick determines which host you can assign (he or she must be a member of at least one of the visitor type's host access groups) and which visitor access groups you can assign (they must be in the visitor type's list of visitor access groups).

object
href: string (url)
This is the href you should use for a visitor type when you create or update a visit.

It is only used for identification: GETting it will 404.

accessGroup: object
The name and href of an access group.

Command Centre will add cardholders to this access group as soon as you add them to a visit of this type. The purpose of this group is to grant access to PDFs for personal data that will help them sign in on the day, such as passport and driver's licence numbers, and photos.

name: string
href: string (url)
hostAccessGroups: object[]
Names and hrefs of more access groups. When you create or modify a visit of this type, its host cardholder must belong to one of these.

A host is the cardholder Command Centre notifies when a visitor arrives, and is ultimately responsible for the visitor while on site.

object
accessGroup: object
name: string
href: string (url)
visitorAccessGroups: object[]
An array of access groups.

Command Centre will add visitors to a visit's 'visitor access groups' when they sign in. Their purpose is to grant visitors access through the site's doors so that they can move around the site.

When creating a visit using this visitor type, you can only pick visitor access groups from this list.

The server puts each group's name and href inside a block called accessGroup, rather than in the root of the array element, to allow for expansion in a future version.

object
accessGroup: object
The name and href of an access group. Command Centre will add the visitors on the visit to this access group when they sign in.

name: string
href: string (url)
Example
{
  "href": "https://localhost:8904/divisions/2",
  "id": "2",
  "name": "Root division",
  "description": "Contains all other divisions",
  "serverDisplayName": "ruatoria.satellite.int",
  "parent": {
    "href": "https://localhost:8904/divisions/2"
  },
  "visitorManagement": {
    "active": true,
    "visitortypes": [
      {
        "href": "https://localhost:8904/api/divisions/2/visitor_types/925",
        "accessGroup": {
          "name": "Visitor access group 1",
          "href": "https://localhost:8904/api/access_groups/925"
        },
        "hostAccessGroups": [
          {
            "accessGroup": {
              "name": "Host access group 1",
              "href": "https://localhost:8904/api/access_groups/938"
            }
          }
        ],
        "visitorAccessGroups": [
          {
            "accessGroup": {
              "name": "Access group 22",
              "href": "https://localhost:8904/api/access_groups/926"
            }
          },
          {
            "accessGroup": {
              "name": "Access group 30",
              "href": "https://localhost:8904/api/access_groups/927"
            }
          }
        ]
      }
    ]
  }
}
Division PATCH and POST example: object
This is an example of a PATCH you could use to update a division, and a POST you could use to create one.

When POSTing, parent is mandatory.

name: string
The division's name. If you supply a name and another division already exists with that name, the call will fail. If you leave it blank in a POST, Command Centre will pick value for you.

description: string
The division's description.

notes: string
A string, able to me much longer than description, suitable for holding notes about the division.

parent: object
An object containing an href to the division entity representing the current division's parent.

Required when creating a new division, because only root divisions can be unparented and you cannot create a new one of those.

href: string (url)
Example
{
  "name": "Long division",
  "description": "Quatermasters",
  "notes": "A very long string.",
  "parent": {
    "href": "https://localhost:8904/api/divisions/2"
  }
}
Item search: object
Calls to /api/items return this object.

results:  Item summary 
A list of item objects, each containing the alphanumeric ID of the item, its name, and its type.

Item summary
next: object
An href to the next page of results. Missing if there are no more results.

href: string (url)
Example
{
  "results": [
    {
      "id": "325",
      "name": "Brick, Eva",
      "type": {
        "id": "1",
        "name": "Cardholder",
        "canonicalTypeName": "cardholder"
      }
    },
    {
      "id": "2707",
      "name": "Brewer, Amy",
      "type": {
        "id": "1",
        "name": "Cardholder",
        "canonicalTypeName": "cardholder"
      }
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/items?pos=2"
  }
}
Item summary: object
/api/items returns an array of these. Because it can contain an item of any type, it only contains some fields that are common to all item types.

id: string
Use this ID in the source filter when requesting events to limit events to those with that source. Card events such as 'access granted', for example, have a door as their source.

name: string
type: object
The type object contains the ID and name of the item's type.

id: string
Use this ID in the type filter when requesting items if you wish to restrict the results to items of a certain type.

name: string
A human-readable name of the item's type, suitable for display.

This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.

canonicalTypeName: string
An unchanging, alphanumeric, short, and hopefully descriptive identifier for the item type.

The set of possible values will grow as time passes but the strings themselves, unless highlighted as temporary, will never change. You can count on an access zone always having a canonical type name of 'accesszone', for example.

Added in 9.00.

serverDisplayName: string
If you are running a multi-server installation and this item is homed on a remote server, this field will contain the name of that server. This field is missing from items that are held on the machine that served the API request. Added in 8.40.

This is a read-only field. The server will ignore it if you send it.

notes: string
Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

href: string
Reserved for internal use. Its value changed in version 9.10.

Example
{
  "id": "325",
  "name": "Brick, Eva",
  "type": {
    "id": "1",
    "name": "Cardholder",
    "canonicalTypeName": "cardholder"
  },
  "serverDisplayName": "ruatoria.satellite.int",
  "notes": "Multi-line text...",
  "href": "string"
}
Item detail: object
A call to /api/items/{id} (added in 8.40) returns one of these. It adds the item's division to the summary object.

Item summary
division: object
id: string
href: string (url)
Example
{
  "id": "325",
  "name": "Brick, Eva",
  "type": {
    "id": "1",
    "name": "Cardholder",
    "canonicalTypeName": "cardholder"
  },
  "serverDisplayName": "ruatoria.satellite.int",
  "notes": "Multi-line text...",
  "href": "string",
  "division": {
    "id": "2",
    "href": "https://localhost:8904/api/divisions/2"
  }
}
Item types: object
Calls to /api/items/types return this object, an array of item types.

itemTypes: object[]
A list of item types.

object
id: string
The alphanumeric ID of the item type. Use this ID in the type filter when requesting items.

name: string
The human-readable name of the item type. These strings will change over time as Gallagher expands its product line, and can change with the server's language settings, so take care if using them for anything except display.

canonicalTypeName: string
An unchanging, alphanumeric, short, and hopefully descriptive identifier for the item type.

The set of possible values will grow as time passes but the strings themselves, unless highlighted as temporary, will never change. You can count on an access zone always having a canonical type name of 'accesszone', for example.

Added in 9.00.

Example
{
  "itemTypes": [
    {
      "id": "1",
      "name": "Cardholder",
      "canonicalTypeName": "cardholder"
    },
    {
      "id": "2",
      "name": "Access Group",
      "canonicalTypeName": "accessgroup"
    }
  ]
}
Item update: object
POSTs and GETs to /api/items/updates return this object. It contains an array of updates and a next link to collect more.

Added in 8.30.

updates: object[]
A list of items and their statuses.

On your first two calls -- the POST and the first GET -- this will contain all the items in your subscription (provided they are the types of item that have statuses). On subsequent GETs it will only contain the items that received status updates since your previous call. Note that an item will be in this array if it received any status update at all, even if the status flags did not change. Prepare to receive updates that do not contain novel data.

This array is not paginated: it could contain every one of the items you put in your POST.

After about 50 seconds the call will time out and return an empty array here.

object
id: string
The item's ID, from the list you sent in the POST that created this subscription.

statusFlags: array
An array of string enumerations (flags) that describe the item's condition in a reliable, machine-readable way.

The item types that the REST API supports (such as fence zones and inputs) have a full set of status flags, described in their own sections of this documentation.

Items that the REST API does not support yet (such as readers) will return error flags if they are in an unusual state, or nothing if they are online and reporting normally. Therefore an empty array is a good sign.

statusText: string
The state of the item in a multi-line string taken from the server's language pack.

status: string
A one-line version of the status, with details removed if necessary to keep it short.

next: object
A link to GET more status updates. Do not wait longer than thirty seconds before using this link or your subscription will expire and you will need to submit another POST to create a new one.

Example
{
  "updates": [
    {
      "id": "508",
      "status": "Controller offline. 62 message(s) pending.",
      "statusText": "Controller offline.\r\n62 message(s) pending.",
      "statusFlags": [
        "controllerOffline"
      ]
    },
    {
      "id": "526",
      "status": "Disarmed.",
      "statusText": "Disarmed.",
      "statusFlags": [
        "disarmed"
      ]
    },
    {
      "id": "530",
      "status": "This Input is Closed.  ",
      "statusText": "This Input is Closed.  ",
      "statusFlags": [
        "closed"
      ]
    },
    {
      "id": "531",
      "status": "Awaiting status from Controller.",
      "statusText": "Awaiting status from Controller.",
      "statusFlags": [
        "controllerUnknown"
      ]
    },
    {
      "id": "532",
      "status": "This Output is Off.  ",
      "statusText": "This Output is Off.  ",
      "statusFlags": [
        "open"
      ]
    },
    {
      "id": "533",
      "status": "Secure.",
      "statusText": "Secure.",
      "statusFlags": [
        "secure"
      ]
    }
  ],
  "next": {
    "href": "https://localhost:8904/api/items/updates?bookmark=3ec613a1-de01c6e_0"
  }
}
Item update subscription: object
Contains a list of item IDs (short alphanums). Send it in the body of a POST to create a subscription to status updates to the items with these IDs.

itemIds: string[] 
string
Example
{
  "itemIds": [
    "506"
  ]
}
