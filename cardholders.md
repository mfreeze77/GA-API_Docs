# Command Centre REST API: Alarms and Events
            API Reference

### Licensing

### Versions

#### Alarms, events, and items API changes on the roadmap

#### Alarms and events API changes in 9.00

#### Alarms and events API changes in 8.90

#### Alarms and events API changes in 8.70

#### Alarms and events API changes in 8.60

#### Alarms and events API changes in 8.50

#### Alarms and events API changes in 8.40

#### Alarms and events API changes in 8.30

#### Alarms and events API changes in 8.20

#### Alarms and events API changes in 8.10

This document describes how you can use the Command Centre REST API to download, monitor, and create events, and monitor and manage alarms.

It has companion documents describing the APIs for
                  Cardholders,
                  PIV cards, and
                  non-cardholder items.

See the
                  events section for examples of how to create events, download events, search for events using various criteria, and receive new events as they arrive.

See the
                  alarms section for the methods you can use to download all current alarms, receive notifications as they change state, and manage them. These methods offer similar alarm-management functions to those in the Premier and Command Centre clients.

Helper methods allow you to look up Command Centre's internal identifiers for cardholders, divisions, and other items, so that you can use those identifiers in search filters.

All of the GETs in this document and the POSTs that manage alarms require the RESTEvents licence. The POST to create a new event requires RESTCreateEvents.

The server will return a 403 if you attempt an operation for which the server is not licensed.

The body of this document clearly indicates when recent features arrived in the API so that readers with older versions of Command Centre know not to expect them.

You can create and delete divisions, and modify some basic fields.

A new event type hasLocation greatly simplifies the monitoring of cardholder movements (
                      introduction,
                      use case ), and

a new location block on events greatly simplifies interpreting them (
                      schema ).

BREAKING CHANGE: the operator must have the 'Create Events and Alarms' privilege in the division of the source item, if your request specifies a source item. Current versions only require that the operator has that privilege on at least one division.

To improve the symmetry between events and alarms, they now carry a field eventType containing the ID and name of their type. The type field, which has different contents for alarms and events, is now obsolete (but still supported).

Alarms contain a new field called event containing a link to the corresponding event, mirroring the existing alarm field coming the other way from an event.

You can request a division's description. This has been possible since 8.50 but was not certified until 2022, so it gets a mention here.

Each event in a page of search results will contain URLs to continue the search after that event. This is a significant benefit to integrations that extract large pages of events and may encounter a problem mid-page, and have to resume without loss or duplication later.

A new privilege 'View Cardholder Events' grants visibility of an event if the operator has the privilege in both the event's division and the event's cardholder's division. This allows a site to create a REST client that can monitor selected cardholders' movements through selected areas, but cannot see any other activity in those areas.

You may now use the relatedItem query parameter to find events that are associated with any item.

Card numbers on Bluetooth (mobile) and PIV card events are now full-length. Mobile numbers are now the phone's ID string, changed from the number that 8.50 returned. Decimal numbers now come out unsigned. Note that neither 8.50's numbers nor 8.60's ID strings are guaranteed unique across phones.

Access events that do not have a card, such as someone entering their user code instead of badging their card, will no longer return a card block.

The server property that turns off
                      client certificate checking changed.

Divisions can return some of their Visitor Management configuration.

You can view any item's notes. You have been able to for a while now, but it was missing from this document.

You can request a division's description.

You may now use the fields query parameter to tailor the fields that come back for events and alarms.

Operator add, modify, and delete
                      events now contain a link to the affected item in a new modifiedItem block, deprecating the accessGroup block for those event types.

All operator events now contain the operator's name at the time.

All events now contain the name of the event's division.

The
                      item search can now filter multiple item types.

Events now show their origin when they have arrived from a remote server (in a multiserver environment).

The
                      items API has methods that let you monitor the status of large numbers of items.

Doors related to guard tour events now appear in an event's door block.

Alarms and events with a related cardholder now show the cardholder's current first and last name in separate fields.

The name field on an alarm or event with a related cardholder is now the cardholder's name at the time of the event, rather than at the time of the request.

You can use the 'fields' parameter to add the 'details' field to an event summary.

- You can create and delete divisions, and modify some basic fields.

- A new event type hasLocation greatly simplifies the monitoring of cardholder movements (
                      introduction,
                      use case ), and

- a new location block on events greatly simplifies interpreting them (
                      schema ).

- BREAKING CHANGE: the operator must have the 'Create Events and Alarms' privilege in the division of the source item, if your request specifies a source item. Current versions only require that the operator has that privilege on at least one division.

- To improve the symmetry between events and alarms, they now carry a field eventType containing the ID and name of their type. The type field, which has different contents for alarms and events, is now obsolete (but still supported).

- Alarms contain a new field called event containing a link to the corresponding event, mirroring the existing alarm field coming the other way from an event.

- You can request a division's description. This has been possible since 8.50 but was not certified until 2022, so it gets a mention here.

- Each event in a page of search results will contain URLs to continue the search after that event. This is a significant benefit to integrations that extract large pages of events and may encounter a problem mid-page, and have to resume without loss or duplication later.

- A new privilege 'View Cardholder Events' grants visibility of an event if the operator has the privilege in both the event's division and the event's cardholder's division. This allows a site to create a REST client that can monitor selected cardholders' movements through selected areas, but cannot see any other activity in those areas.

- You may now use the relatedItem query parameter to find events that are associated with any item.

- Card numbers on Bluetooth (mobile) and PIV card events are now full-length. Mobile numbers are now the phone's ID string, changed from the number that 8.50 returned. Decimal numbers now come out unsigned. Note that neither 8.50's numbers nor 8.60's ID strings are guaranteed unique across phones.

- Access events that do not have a card, such as someone entering their user code instead of badging their card, will no longer return a card block.

- The server property that turns off
                      client certificate checking changed.

- Divisions can return some of their Visitor Management configuration.

- You can view any item's notes. You have been able to for a while now, but it was missing from this document.

- You can request a division's description.

- You may now use the fields query parameter to tailor the fields that come back for events and alarms.

- Operator add, modify, and delete
                      events now contain a link to the affected item in a new modifiedItem block, deprecating the accessGroup block for those event types.

- All operator events now contain the operator's name at the time.

- All events now contain the name of the event's division.

- The
                      item search can now filter multiple item types.

- Events now show their origin when they have arrived from a remote server (in a multiserver environment).

- The
                      items API has methods that let you monitor the status of large numbers of items.

- Doors related to guard tour events now appear in an event's door block.

- Alarms and events with a related cardholder now show the cardholder's current first and last name in separate fields.

- The name field on an alarm or event with a related cardholder is now the cardholder's name at the time of the event, rather than at the time of the request.

- You can use the 'fields' parameter to add the 'details' field to an event summary.

- Incoming events

- Events now show their related
                    access groups and doors for external event types.

### API key

Clients authenticate by including a pre-shared API key in the Authorization header of each request. Command Centre generates an API key when you create an endpoint for your clients to connect to. Search the Configuration Client online help for 'REST API' for how do do that.

The API key will be in the format XXXX-XXXX-XXXX-XXXX-XXXX-XXXX-XXXX-XXXX and should be in the header following an authorisation method of GGL-API-KEY and a space. Both should be in upper case. For example:

Authorization: GGL-API-KEY C774-B01F-D695-AA4B-215F-A158-AC22-ADEB

Early versions of Command Centre allowed you to omit the 'GGL-API-KEY'. Later versions of CC and all versions of the API gateway will reject you with a 401 if you do that.

Depending on Command Centre's site configuration, its REST API may also require a client certificate with each request. In versions up to and including 8.40 this was controlled by a flag labelled 'Do not require pinned client certficates' on the 'Web Services' tab of the server properties. In 8.50 that flag's label changed to 'Enable REST Clients with no client certificate', and its behaviour changed slightly when turned on.

If off, for all versions of CC, an incoming request's certificate has to match the thumbprint on its API key's REST Client item, and REST Client items with a blank thumbprint field do not work at all. This is how it ships, and it is the recommended way of running a production server.

If 'Do not require pinned client certificates' was turned on in versions up to and including 8.40, the server did not check any client certificates. If that flag is turned on in 8.50, now relabelled 'Enable REST Clients with no client certificate', it still does not check the client certificate if the thumbprint field of the matching REST Client item is blank, but if the Client item has a thumbprint, the server will reject connections with the wrong certificate.

See the Configuration Client help for instructions on where to enter REST Client thumbprints.

Also note that if IP filtering is enabled on the REST Client item in Command Centre, the API will only accept connections from the IP address ranges configurated into that client item.

If a connection attempt fails, the server will return a 401 and raise an event containing its reason for refusing the request. If that happens too often, the server will stop reporting each offence and will instead create a summary alarm at a much lower rate. The details of the alarm tell you how many failed attempts there have been since the start of the flood. The server will stay in this mode of reduced reporting until several minutes pass without a failed connection attempt.

The current failure limit is ten errors inside one minute. After that you will receive one "a large volume of requests has been denied" alarm every minute while the failures continue until five minutes passes without a failure.

The most common queries we receive from our integrators relate to their certificate handling. If your client's HTTP client library complains about certificates the first thing to check is Command Centre's alarm list. If there are 'invalid client certificate' alarms there, your client is not sending the certificate CC is expecting. If there are no alarms then the client is most likely rejecting the server's certificate.

The API's reference documentation divides into:

The alarms and events APIs date back to v7.80. They let you download, monitor, and create events, and download, monitor, and manage alarms.

The alarms and events documentation also covers API calls that support divisions and items as they relate to events, as well as bulk status monitoring.

Despite its name this section does not cover
                        mass-monitoring item status.

This is a self-referencing REST API that follows the principles of HATEOAS. Other than the initial GET to /api when it first connects, your source code should not contain any URLs, as they are subject to change. You should append the query parameters this document describes for operations such as filtering and searching, but everying in the path should come from the results of /api or pages linked from it.

/api only shows licensed API calls.

Be prepared to append query parameters to URLs that already have their own: do not assume that you can simply add a question mark and your parameters.

### If your privileges do not seem to be working

First, some background on operators, operator groups, and divisions.

To determine your REST client's privileges, the server starts by searching the list of REST Client items in its configuration for the one with the API key in the Authorization header of your request. Assuming it finds one, it takes the cardholder from that REST Client item.

To be any use at all that cardholder must be a member of at least one operator group. Being in an operator group makes a cardholder an
                  operator.

After the server has your operator, it needs to check whether that operator has access to the items or events in the request. To explain that, it is necessary to cover divisions.

Aside from some special items such as day categories, every item in Command Centre is in a
                  division. Divisions are hierarchical, with all divisions (but the root) being a child of another. Multi-server installations have one division tree per server.

Every event and alarm has a division. It is usually the division of the source item. Card events or 'forced door' alarms, for example, typically use a door as a source item; when an operator modifies an item, the event recording that change uses that operator's workstation as the source item; the alarm that the server generates when you send a bad API key uses the server item as the source.

An event's division is not always its source's division. 'Card activated' events have the server as a source item, but take on the cardholder's division so that operators who can see the cardholder can also see when the server activates their cards.

To link privileges to items and events, an operator group contains a list of privileges and a list of divisions. Its operators enjoy those privileges on all the items, events, and alarms that are in those divisions.

Having a privilege on a division also grants an operator that privilege on that division's descendants. Therefore an operator with a privilege on the root division has that privilege on all that server's items and events.

A common misconception is that the division an operator is in, or the division an operator group is in, have a bearing on the operator's privileges. They do not. It is all about the divisions in the operator group's 'Operator privileges' list.

First, to protect Command Centre from accident and malice, you should strive to grant your REST clients the fewest and lowest-level privileges you can. Do not give them 'advanced user'. Reaching a point where you do not receive the results you want is a good thing: it means you have screwed things down a little too tightly.

If changing privileges in Command Centre does not seem to make any difference, remember to push the 'Refresh operator privileges' button in the properties window of your REST Client item.

Here are some general rules that may help if you are still not receiving the results you expect.

Receiving a 403 from a GET means the server may not have a license for the retrieval you are attempting or your operator does not have the privilege to view that object. If it is a licensing problem, the body of the response will describe it.

Receiving a 403 from a PATCH, POST, or DELETE means your operator does not have the necessary privilege to perform that operation. You need one of the privileges beginning with 'Edit', 'Modify', or 'Create'.

Receiving a 404 when trying to get one item or event means it either does not exist or your operator does not have the privilege to view it.

Receiving a 200 and an empty result set from a search means you are licensed for that search but your operator could not see any items or events that matched. One possible cause is that your operator is not licensed to see
                      any items or events of that kind. Make sure that one of the operator groups that your operator is in has a 'View' privilege such as 'View events' or 'View cardholders' - whatever is appropriate. If it does, and you have hit the button mentioned above, then check that the divisions on the operator group contain the items you expect. If it is events you are searching for, check the source item on those events either by using a REST operator with 'View events' on the root division or by looking at them in one of the thick clients.

- Receiving a 403 from a GET means the server may not have a license for the retrieval you are attempting or your operator does not have the privilege to view that object. If it is a licensing problem, the body of the response will describe it.

- Receiving a 403 from a PATCH, POST, or DELETE means your operator does not have the necessary privilege to perform that operation. You need one of the privileges beginning with 'Edit', 'Modify', or 'Create'.

- Receiving a 404 when trying to get one item or event means it either does not exist or your operator does not have the privilege to view it.

- Receiving a 200 and an empty result set from a search means you are licensed for that search but your operator could not see any items or events that matched. One possible cause is that your operator is not licensed to see
                      any items or events of that kind. Make sure that one of the operator groups that your operator is in has a 'View' privilege such as 'View events' or 'View cardholders' - whatever is appropriate. If it does, and you have hit the button mentioned above, then check that the divisions on the operator group contain the items you expect. If it is events you are searching for, check the source item on those events either by using a REST operator with 'View events' on the root division or by looking at them in one of the thick clients.

You can specify the fields you want in the results of a query. This lets you:

You do this using the query parameter fields to the search, detail, and updates pages. It takes a comma-separated list of field names. They are case-sensitive: copy them carefully from this document. The special field name defaults adds in the fields the server would have sent had you not set the fields parameter at all.

Example GETs:

/api/cardholders?name=Smith&fields=defaults,cards,competencies will list all cardholders with 'Smith' in the first or last name, showing the fields you normally get on a search page plus their cards and competencies.

/api/access_groups/123?fields=children,cardholders will show just the child groups of access group 123 and a link to retrieve its cardholders - nothing else.

/api/outputs?fields=name,shortname,id will show the name, short name, and ID of your outputs.

/api/access_zones?fields=name,doors will show the name and attached doors of your access zones.

/api/alarms?fields=time,message,details will return the time, message text, and details text of your alarms. Not only does that save a lot of data by removing default fields, but it adds the details field which does not normally come out of an alarm search.

Each controller has different optional fields - see their sections under Operations.

- save calls by adding more fields to search results, which are terse by default

- save bandwidth by putting fewer fields on detail pages, which are verbose by default, and

- retrieve more data by adding fields to detail pages that are not normally there, such as an item's status, short name, and notes.

- /api/cardholders?name=Smith&fields=defaults,cards,competencies will list all cardholders with 'Smith' in the first or last name, showing the fields you normally get on a search page plus their cards and competencies.

- /api/access_groups/123?fields=children,cardholders will show just the child groups of access group 123 and a link to retrieve its cardholders - nothing else.

- /api/outputs?fields=name,shortname,id will show the name, short name, and ID of your outputs.

- /api/access_zones?fields=name,doors will show the name and attached doors of your access zones.

- /api/alarms?fields=time,message,details will return the time, message text, and details text of your alarms. Not only does that save a lot of data by removing default fields, but it adds the details field which does not normally come out of an alarm search.

What you can do with the fields query parameter on an events method has improved through the versions, so explaining it is a topic in itself.

In 8.40 and later the values you can list are the same as the field names in the
                  details page, plus the special value for a personal data field described below. You can pick whichever fields you want, including defaults, though we urge you to only list the fields you need. Anything you wrote for 8.30 or earlier will work in 8.40 as it did before.

In versions up to 8.30 you can only add fields to the event summary and details pages, not remove them. If you send the parameter it must start with fields=defaults. That gives you the default set. What you can add after that depends on the version of Command Centre you're calling.

In 8.10 and earlier the only field you can add is cardholder.pdf_XXXX, where XXXX is the ID of a PDF. Find that ID with a query to the
                  PDFs controller. Don't forget a separating comma between it and defaults.

That will add a cardholder's PDF to the events that are related to them.

You can only pick one PDF. It will only appear on events that have a related cardholder, such as access events, because without a cardholder there is no PDF. The security model applies too, so it will only appear if your REST operator has the appropriate privileges on that cardholder and PDF.

In 8.20 and 8.30 you can also add details. Make sure you have a separating comma after defaults or cardholder.pdf_XXXX.

An event detail string can be kilobytes long so we left it optional. It is the only field in the details page that is not in the search results, by the way, so if you are running 8.20 or later you can add it to your search results and you will not need the details page.

### Alarm use cases

#### Downloading and managing unprocessed alarms

#### Staying up to date

Use these methods to download, monitor, and manage Command Centre alarms.

After getting all the current alarms using the process above, follow the link at updates.href. It will block until there is a new alarm or a change to an existing alarm.

- GET /api

- Follow the link at features.alarms.alarms
↪. You will receive up to 100 alarms, each containing links to its management functions.

- If step 2 returned results and there is a link at next.href, follow it and repeat.

This returns the current list of unprocessed alarms. The result will contain no more than 100 alarms; you should follow the next link, if it is present, to collect more.

You can tell when you have loaded all the current alarms because there will not be a next link. Instead, there will be an updates link, which you may then follow to long-poll for live updates to alarms.

The alarm summary only contains unprocessed alarms, but you can still access processed alarms by finding them in the
                    event summary and following its alarm.href link to the alarm details.

Unlike the corresponding method that retrieves events, this call does not take query parameters to filter its results. You can limit the fields it returns, but it will always return every alarm that your operator can view. If you wish to restrict it to alarms in certain divisions, give your operator permission to view alarms in only those divisions.

Do not code this URL into your application. Take it from alarms.alarms.href in the results of GET /api.

Sets the fields you want in your results. Separate fields with commas.

New to 8.40.

Success

The site does not have a RESTEvents licence.

Long poll this link for live updates to alarms. If alarms occurred or changed since the previous call, no matter how long ago that was, it will return them immediately. If there are no updates pending, it will wait until one arrives. If none arrive before a timeout passes (about 30 seconds) the updates array in its response will be empty.

Whether or not the response contained updates the client should follow the next link to wait for the next updates. If your server receives a lot of alarm updates, wait some time between calls to reduce its load. You will not miss any updates: each response contains everything that happened since the previous call.

Command Centre does not tell you which alarms were added, removed, or modified. It is up to you to match the incoming alarms against your own internal alarm list and determine the differences.

Do not code this URL into your application. Take it from alarms.updates.href in the results of GET /api, or from updates in the results of GET /api/alarms, or from next in the results of this call. Do not attempt to interpret or set the id query parameter in the URL, as it tracks your progress through the alarm history.

Assuming you got the URL for this call from the response from another call, and your operator therefore has the privileges required to view alarms, this should always complete succesfully.

Sets the fields you want in your results. Separate fields with commas.

New to 8.40.

Success

The site does not have a RESTEvents licence.

Full details for an alarm. Follow the href in the alarm summary to get here.

The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

Sets the fields you want in your results. Separate fields with commas.

New to 8.40.

Success

The alarm ID is invalid or you do not have privileges for the alarm.

Mark the alarm as viewed. Follow the view link in the alarm summary to get here.

Optional comment.

The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

Success

Alarm ID is invalid or you do not have privileges for the alarm.

Follow the comment link in the alarm summary to get here.

The comment string.

The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

Success

Alarm ID is invalid or you do not have privileges for the alarm.

Follow the acknowledgeWithComment or the acknowledge link in the alarm summary to get here.

Optional comment.

The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

Success

Alarm ID is invalid or you do not have privileges for the alarm.

Follow the processWithComment, forceProcess, or process link in the alarm to get here.

Optional comment.

The ID of the alarm. This is not really a parameter you should insert into your request. Instead you should get the URL of this call from other results: the alarm summary or details, in this case.

Success

Alarm ID is invalid or you do not have privileges for the alarm.

### Licensing

These methods provide access to the Command Centre divisions that are available to the REST client. Call /api and use the link at features.{your_feature}.divisions.href to retrieve the divisions in which the REST operator has privileges for that feature. Pick out the ones of interest and use those IDs in event, alarm, or item searches if you don't want the search to scan everything.

Or, you may end up here by following a division's link from a reception item when you need to retrieve the visitor management configuration for that reception's division.

Every REST licence enables the divisions controller: RESTEvents, RESTCreateEvents, RESTCardholders, RESTStatus, and RESTOverrides.

Creates a new division.

The POST expects a document in the same format as the
                    the division detail but with far fewer fields. An example is
                    this POST example. The only mandatory field is parent.

When successful it returns a location header containing the address of the new division.

Note that you can only create one division per POST.

Do not code this URL into your application. Take it from the results of GET /api.

/api only shows API features for which you have the necessary licence.

Creating a division requires the RESTCardholders licence.

Creating divisions is new to 9.00.

The only required field here is the new division's parent. All divisions (except the root) must have a parent.

Success. Check the response body for feedback about your request.

The body of the POST did not describe a valid division. This includes not specifying a parent division. Or it may be that you tried to give the new division the same name as an existing one. Or you may have tried to set the new division's parent to a division that is not visible to you.

See the body of the response for help.

The operator does not have a privilege on the parent division that allows creating divisions inside it ('Configure Site' or 'Edit Divisions'), or the server has reached its licensed limit of divisions.

The functions inside /api/divisions/ retrieve the divisions in which the operator can perform other functions. They all return the same data structure.

For example, /api/divisions/view_events retrieves the list of divisions in which the REST operator has privileges to view events, and /api/division/view_alarms does the same for alarms.

Do not code these URLs into your application. Take them from the results of GET /api. For the events and alarms examples, the links will be at events.divisions.href and alarms.division.href.

/api only shows API features for which you have the necessary licence.

Sets the maximum number of divisions to return per page.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Return these fields instead of the default set. The values you can list are the same as the field names you would see in the results, plus visitorManagement, which does not come out by default. Use it to specify the fields you want in your results. Separate values with commas.

Treat the string matches as case-sensitive.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success

The site does not have a REST licence.

Details of a division. Follow the href in a division summary to get here.

The ID of the division.

Return these fields instead of the default set. The values you can list are the same as the field names you would see in the results, plus visitorManagement, which does not come out by default. Use it to specify the fields you want in your results. Separate values with commas.

Treat the string matches as case-sensitive.

Success

The site does not have a REST licence.

That is not the href of a division or you do not have privileges to read divisions (View Site, Configure Site, Edit Divisions, or Advanced User).

This is the call you use to update a division's name, description, notes, or parent.

The PATCH expects a document in the same format as the
                    the division detail but with fewer valid fields. An example is
                    this PATCH example.

Patching divisions is new to 9.00.

There are no mandatory fields.

Success. The response body will contain feedback from the server about your PATCH.

Success.

The body of the PATCH did not describe a valid division. See the body of the response for help on what went wrong. It may be that you tried to use the name of another division: no two items of the same type can have the same name. Or you may have tried to set the division's parent to a division that is not visible to you.

The operator has a privilege that allows viewing the division but not modifying it, or you tried to set a parent on the root division, or you tried to set the parent to a division you cannot configure, or the server is missing the necessary licence.

You need either the 'Configure Site' or 'Edit Divisions' privilege on the division you are changing. You also need it on the new parent, if you are changing that.

That is not the URL of a division or the operator does not have the privilege to view that division. This probably means you have built the URL yourself instead of taking it from the results of a
                        GET.

The item is locked for editing by another operator. The body of the response will tell you which operator is holding the lock.

This call removes a division from Command Centre.

Deleting divisions is new to 9.00.

Success.

Deleting the division failed. This happens when the division is not empty or is still being used by another item (such as an operator group).

The operator has the permission to view the division but not delete it, or the server is not licensed for the operation.

That is not the URL of a division, or the operator is not privileged to view it.

### Location events in 9.00

### Event use cases

### Licensing

### Efficiency tips

#### Downloading the entire event database

#### Downloading the most recent events

#### Reporting on events over a period.

#### Receiving new events as they occur, starting from now

#### Receiving new events as they occur, starting from now, with no long polls

#### Receiving new events as they occur, starting in the past

#### Searching for events related to particular cardholders

#### Searching for events that indicate location or movement

#### Searching for events coming from other items

#### Creating a new event

Use the GET methods in this API for historical searches or to stay up to date with new events as they occur. Use the POST method (added in version 8.10) to create events of your own.

There are no PATCH actions on events, because they are immutable. If an event is also an alarm it carries some changeable state and a log of activity and comments, but the underlying event never changes.

The API only returns events that are still in Command Centre's database. It will not return events that have been removed, even if they are in an archive file.

Command Centre has about 80 event types that occur when somebody authenticates at a device, usually by badging a card. Some of those event types also contain the access zones that the person started and finished in. Version 9.00 introduced some improvements that presented the cardholder and the two zones to API clients in a consistent way:

A new hasLocation event type lets you filter for only those events that reveal a cardholder's location. Use it in the type query parameter as you would normally use an integer event type. Gallagher will update this as new versions add more event types.

A new location block on events of those types that contains fields containing the cardholder and their location.

For more on using these new features, see the relevant use case below (search for 'location').

If you then wish to stay up to date, switch to following the link at updates.href
↪ in the results. That call will block until more events arrive, or a minute passes (approximately).

Remember that some remote systems take their time sending events to Command Centre, so do not be too hasty running your reports. If you fire them off at the stroke of midnight, you may miss events that occurred before midnight but have not yet arrived at Command Centre.

GET /api

Follow the link at features.items.items.href
↪, adding name="your_cardholder_name"&type=1 to the query after the appropriate separator (? or &).

The 'items' controller necessary for that step is available with the RESTEvents licence. If you also have the RESTCardholders licence you could use the link at features.cardholders.cardholders.href
↪ instead, adding a separator and name="your_cardholder_name".

In either case, remove the quotes if you want a substring search and can handle more than one cardholder in the results.

Extract the item ID of your cardholder or cardholders from that page, repeating as necessary for additional cardholders.

Follow the link on the /api page at features.events.events.href
↪ appending the separator and cardholder=XX or cardholder=XX,YY,ZZ with the cardholders' IDs.

To further improve the efficiency of your search, filter by event types and a time range.

If your server is running 8.60 or earlier, use the search filter types=20001,20002,20003,20047,20107,42415. That is not a complete list of movement event types but it includes the popular ones.

If your server is running 8.70-8.90 inclusive, give your operator the 'View cardholder events' privilege instead of 'View events'. Then request all events, and the server will only send you movements.

If your server is running 9.00 or later, the 'View cardholder events' privilege is still an excellent idea but you can also use the search filter types=hasLocation&fields=location (with a leading ? or & of course, and more fields if you need them). That will limit the results to location events. Then look at the type field inside the location block:

If you are simply after a person's location, use the afterLocation field inside the location block. That could be an access zone or division if they moved or attempted to move, or a workstation or alarms terminal if they logged into one of those. Future versions of Command Centre or customisations might add more.

If you are after movements, which happen when a door grants a person access or an operator moves them on a tag board or via the API, and happen to some types of visitors when their host moves, ignore all events except those that have location.type of moved. Look at the afterLocation to see where they landed. It will be an access zone or (for departing visitors) a reception. There will also be a beforeLocation if the door had two zones configured on it, in case you're interested in where they came from.

If you are after denials, which happen when a person authenticates but has their passage blocked, look at the events that have location.type of denied. Like 'moved'-type events, location.afterLocation will show where they were. The difference is that they started there too.

Each of those fields is covered in the
                schema definition.

Events such as 'access granted' and 'zone count maximum' come from doors and access zones. To search for them, follow the same process as the previous use case (getting cardholder events) but use the source filter parameter instead of cardholder. It will limit the results to just those events that came from the items you gave in the query. If you have the RESTStatus licence (added in v8.00), you can use
                that API to search for access zones, alarm zones, fence zones, macros, outputs, doors, and (in 8.10) inputs. If you do not, or if your source is not one of those types, use the
                items API with a suitable type filter.

You can also use the type parameter to limit the events to particular event types. By doing this you can (for example) subscribe to 'access granted' events from a collection of doors.

New in v8.70, you can also filter by relatedItem. Use this to find events related to item or items regardless of the type.

There are some rules around creating events, so you should first have a careful read of the
                POST documentation.

The GETs that collect alarms and events require the RESTEvents licence.

The POST that creates events requires RESTCreateEvents.

The GET to collect event types requires RESTEvents or RESTCreateEvents.

Use search parameters to reduce the filtering burden on the server.

When downloading a significant number of events, leave top at 1000 or more, provided your client can handle results over a megabyte (events are around 1 KB each). Performance tests have shown that throughput decreases dramatically if top is too low.

If using 8.40 or later, use its field query parameter to cut back on the fields the server sends to you. Not only will it save bandwidth, but it will save the server looking up all those values and serialising them for you.

If you are using the updates link to keep up to date with events, sleep between calls for as long as your requirements allow. Doing that will improve the likelihood of your collecting more than one event when it is busy.
                    For example, if Command Centre is generating ten events per second and you do not sleep between REST calls, you will be calling updates ten times per second for one event each time. However if you sleep for two seconds after each call you will receive 20 events at a time, saving CPU and I/O.

- A new hasLocation event type lets you filter for only those events that reveal a cardholder's location. Use it in the type query parameter as you would normally use an integer event type. Gallagher will update this as new versions add more event types.

- A new location block on events of those types that contains fields containing the cardholder and their location.

- GET /api

- Follow the link at features.events.events.href
↪. You can add query parameters to alter the search. If you are running 8.70 or later and there is a danger that you might encounter a problem while processing a batch of events you should also use fields to get the next link for each event. Note that this is different from the next link you receive at the end of each result set.

- Process the events you receive in that call. If you asked for the next field, write the link for each event you successfully process to disk before attempting the next one. Then, if the worst happens, your client can read that link from disk and pick up where it left off.

- If there were results, follow the link at next.href and repeat.

- Get /api

- Take the link at features.events.events.href
↪ and append previous=true&top=20. Add the appropriate query separator ? or & first, depending on whether there is a query parameter in the URL already, and change the 20 as appropriate. The default is 1000, which is probably more than you want.

- Process the events you receive in that call.

- Follow the link at previous.href to get earlier events, or next.href or updates.href for later events. The last is a long poll, which means that if no events are ready when you make the call it will block until new events arrive.

- GET /api

- Follow the link at features.events.events.href
↪ appending after=2017-01-01Z&before=2017-02-01Z or whatever timestamps are appropriate (after the correct query separator, ? or &).

- GET /api

- Follow the link at features.events.updates.href
↪ adding query parameters containing your search terms. The call will block until at least one matching event arrives at the server.

- Process the events you receive in that call. Often there is only one: the first that arrived after you made the call.

- Sleep to reduce load on the server.

- Loop, following the link at updates.href after processing each batch of events. It will return immediately if there are new events waiting, or it will block until new events arrive. Follow the advice in
                  downloading the entire event database about keeping track of your position if you might encounter a problem writing events mid-batch. Note that that advises using the next field, but you will be using the updates field, which is the same thing in long-poll form.

- GET /api

- Follow the link at features.events.events.href
↪ and append previous=true&top=1. Add the appropriate query separator ? or & first, depending on whether there is a query parameter in the URL already. The call will return one event (which you can ignore) and a next link you'll need later.

- Sleep and loop, following the link at next.href after processing each batch of events. It will return immediately whether or not there are new events waiting. Follow the advice in
                  downloading the entire event database about keeping track of your position if you might encounter a problem writing events mid-batch.

- GET /api

- Follow the link at features.events.events.href
↪ appending the query parameter after=2021-05-08Z (or whatever timestamp is appropriate).

- Process the events you receive in that call.

- Sleep to reduce load on the server.

- Loop, following the link at updates.href after processing each batch of events. It will return immediately if there are new events waiting, or it will block until new events arrive. Follow the advice in
                  downloading the entire event database about keeping track of your position if you might encounter a problem writing events mid-batch. Note that that advises using the next field, but you will be using the updates field, which is the same thing in long-poll form.

- GET /api

- Follow the link at features.items.items.href
↪, adding name="your_cardholder_name"&type=1 to the query after the appropriate separator (? or &).
The 'items' controller necessary for that step is available with the RESTEvents licence. If you also have the RESTCardholders licence you could use the link at features.cardholders.cardholders.href
↪ instead, adding a separator and name="your_cardholder_name".
In either case, remove the quotes if you want a substring search and can handle more than one cardholder in the results.

- Extract the item ID of your cardholder or cardholders from that page, repeating as necessary for additional cardholders.

- Follow the link on the /api page at features.events.events.href
↪ appending the separator and cardholder=XX or cardholder=XX,YY,ZZ with the cardholders' IDs.

- If you are simply after a person's location, use the afterLocation field inside the location block. That could be an access zone or division if they moved or attempted to move, or a workstation or alarms terminal if they logged into one of those. Future versions of Command Centre or customisations might add more.

- If you are after movements, which happen when a door grants a person access or an operator moves them on a tag board or via the API, and happen to some types of visitors when their host moves, ignore all events except those that have location.type of moved. Look at the afterLocation to see where they landed. It will be an access zone or (for departing visitors) a reception. There will also be a beforeLocation if the door had two zones configured on it, in case you're interested in where they came from.

- If you are after denials, which happen when a person authenticates but has their passage blocked, look at the events that have location.type of denied. Like 'moved'-type events, location.afterLocation will show where they were. The difference is that they started there too.

- GET /api

- POST to the link at features.events.events.href
↪.

- The GETs that collect alarms and events require the RESTEvents licence.

- The POST that creates events requires RESTCreateEvents.

- The GET to collect event types requires RESTEvents or RESTCreateEvents.

- Use search parameters to reduce the filtering burden on the server.

- When downloading a significant number of events, leave top at 1000 or more, provided your client can handle results over a megabyte (events are around 1 KB each). Performance tests have shown that throughput decreases dramatically if top is too low.

- If using 8.40 or later, use its field query parameter to cut back on the fields the server sends to you. Not only will it save bandwidth, but it will save the server looking up all those values and serialising them for you.

- If you are using the updates link to keep up to date with events, sleep between calls for as long as your requirements allow. Doing that will improve the likelihood of your collecting more than one event when it is busy.
                    For example, if Command Centre is generating ten events per second and you do not sleep between REST calls, you will be calling updates ten times per second for one event each time. However if you sleep for two seconds after each call you will receive 20 events at a time, saving CPU and I/O.

#### Usable event types

#### Usable source items

#### Related items

#### Action plans

#### Alarm instructions

#### When events become alarms

Use this method to create an event in Command Centre v8.10 or later.

Do not code this URL into your application. Take it from events.events.href in the results of GET /api.

Each field has particular rules and has its own effects on the event and subsequent reports, and misconfiguration (such as inadvertently causing a macro to run itself) can land you in real trouble, so have a good look at the documentation below and the example
                    POST body.

Events are immutable: you cannot PATCH or DELETE them after you create them.

You must supply an
                    event type. 8.10 ships with 30 external event types you can use, each in its own event type group.

You can create 970 of your own event types using the External Event Type Configuration Utility, a separate Windows application that lets you create external event types and make them appear on items' Event Response and Alarm Instructions tabs in the Configuration Client. You will find the release note for that utility in the Documentation folder on the installation media.

1000 event types sounds like a lot, but be aware that you cannot delete event types, and the only thing you can modify on an existing event type is its name. Their event type group and item types are permanent once you save them from the utility. Please plan carefully, and take backups!

Every event needs a source item. You can let the server pick one for you (it will use the REST Client item identified by your API key) or you can use any item that has an 'Event Response' tab in its Configuration Client window (which is most of them). Our existing integrations use items like doors, external system items, and cameras.

If you allow the server to default to your REST client item as the source, or if specify it yourself, your operator must have 'Create events and alarms' in at least one division for the call to succeed. Any division will do. Otherwise you'll receive a 403.

If you specify a source item to a server running 8.90 or later and that item is not your REST Client item, your operator must have the 'Create events and alarms' privilege in that item's division. In older versions it was enough for your operator to have that privilege in any division.

Cardholders cannot be event sources because they do not have an 'Event Response' tab, but...

Along with a source, you can link other items to the events you create. If you link a cardholder, for example, your events will show on an activity report generated for that cardholder. To link an item your operator must have a privilege that allows viewing it ('View cardholders' for cardholders or 'View site' for most other item types).

An event can fire an action plan, which will

Macros are extremely powerful, and a thorough treatment requires more room than we have, so it is sufficient to say that you should not aim for a dramatic first test. A good first result is to turn on a virtual output made for the purpose. Just make sure that it will not trigger another macro because it is possible to create loops, causing havoc.

You do not pick an action plan to run when you POST your event. Command Centre does that in three steps:

Once it has found the action plan to run, the server will assign the event its priority (if you did not specify a priority yourself) and run the macro if there is one on the 'Command Centre' tab of the action plan's property page in the Configuration Client. The server will not use the configuration from the other tabs.

It is not possible to submit an event with priority zero, but it is possible to submit an event with no priority, and have the action plan assign it priority zero. This will run the macro on the action plan then drop the event before it reaches the database.

Alarm instructions are marked-up text fields that Command Centre presents to security personnel when events occur. Picking an alarm instruction to use follows the same decision path as picking an action plan: Command Centre looks at the configuration of the source item first, and finding nothing there will turn to the source item's alarm zone, and finally to the server properties. If all three are unset the operator will not receive any special instructions.

So, what an operator sees when your event arrives on their board depends on the priority and the event source.

After the server has established an event's priority, either from the body you POSTed or the action plan, it looks at the Event Priorities tab of the server properties. There is a slider there that sets the level above which an event becomes an alarm. By default it is set to two, meaning that any event with a priority of two or higher will appear as an alarm.

- set the priority if the REST client did not set one in the body of the POST, and

- run a macro on the server.

- The server looks at the configuration of the source item in the Event Response tab of its property page in the Configuration Client. If there is an entry for the event type that is not "use default", Command Centre will fire that action plan and skip the next steps. In versions older than 8.30 the control is per event group, not per event.

- Since the server did not find an action plan on the item, it tries its alarm zone. Specifically, the Event Defaults tab of the alarm zone's property page in the Configuration Client. Again the control is per event group not per event type in older versions. If the event's action plan is not 'use default', the server fires that action plan and goes no further.

- Since the server did not find an action plan on the item or its alarm zone, it looks at the configuration in the Event Defaults tab of the server properties. There is always an action plan there, even if it does nothing more than set the priority of the event.

You can specify many things on an event but the only mandatory field is the type. When you are developing, start with just that.

Success.

Success with no event created, probably because the priority in the action plan was zero.

The parameters are invalid. Check the body of the response for an error message.

8.90 and later will reject the POST with a 400 if your event specifies a source item that is not your REST Client item and your operator does not have 'Create Events and Alarms' on its division.

The site does not have the RESTCreateEvents licence (in which case the body of the response will say so) or operator does not have the 'Create Events and Alarms' privilege.

This returns the next batch of events matching the supplied filters starting at the beginning of the database, or at the time specified by the after parameter.

For its correct use in various scenarios, see the
                    use cases.

By default the result will contain no more than 1000 events; for efficient transfer of large numbers of events you should increase this with the top parameter in the request URL.

Each response will contain a next and an updates link. Following the next link will return the next batch of events, or an empty list if there are no more available. Following the updates link will also return immediately if more events are available, but if there are none, it becomes a long poll. It will will block until an event is available that matches the specified filters, or a timeout passes.

Do not code this URL into your application. Take it from events.events.href in the results of GET /api.

Sets the maximum number of events to return per page.

Restricts events to those that occurred at or after this time.

The server accepts extended ISO-8601 time stamp formats. There must be hyphen separators in the date and colons in the time, and a T separating the two.

For predictable results you should also add a timezone specifier. A Z means UTC and is recommended, but +hh, +hhmm, and +hh:mm also work. Note that you need to encode plus-signs as %2b in a URL.

If you omit the time, the server will assume midnight. If you omit just the seconds, the server will take it to the top of the minute, :00.

Restricts events to those that occurred before this time. Events that occurred at this exact time (to the second) will not appear in the results. For example, to collect all events that occurred on 1 January 2017, use after=2017-01-01Z,before=2017-01-02Z. You will not receive any events from 2 January.

The after parameter (above) describes the time stamp formats that the server accepts.

Restricts events to those whose source item has this ID. Separate multiple IDs with commas. Use the
                        items API to search Command Centre's items.

Restricts events to those whose type has this ID. Separate multiple IDs with commas.

The results may include other event types if you also use the group parameter, below.

Use the
                        event groups call to see all event types and groups.

In version 9.00 and later use the special value hasLocation to include all event types that can reveal a cardholder's location or access zone. In 9.00 it is a set of about 80 event types; it may be more in future versions. If you use hasLocation you probably also want to ask for the location block using the fields query parameter.

Restricts events to those with this event group ID. Separate multiple IDs with commas.

group is ORed with type. In fact it is shorthand for type= followed by the IDs of all the event types in the event type groups you list, and the search uses the union of the two lists.

Use the
                        event groups call to see all event types and groups.

Restricts events to those associated with the cardholder that has this Command Centre ID. Separate multiple IDs with commas.

Example: cardholder=325

Restricts events to those in this division (including its child divisions). Separate multiple IDs with commas.

Example: division=2,101

A more secure option is to set the operator's privileges so that it only has access to those divisions.

Restrict events to those associated with the item that has this Command Centre ID. Separate multiple IDs with commas.

Example: relatedItem=3,102

Sets the fields you want in your results.

This is the list of valid fields for 8.40, but it is different for older server versions, so check with the topic
                        on event field specifiers.

Returns the newest events rather than the oldest. Without this option the API will return events starting from the epoch, but if you set it to true the server will return the most recent events, the last of which will be the latest to arrive at the server.

In both cases you can move backward and forward in arrival time with the 'next' and 'previous' links.

Restricts events to those with event IDs greater than this parameter (or less than and including, if you set previous=true).

INTERNAL USE ONLY. This is how Command Centre tracks the events you have seen already. Do not set it yourself. Retain the 'next' or 'updates' link in your application instead.

Success

The site does not have a RESTEvents licence.

Poll this link to receive new events that match the specified filters. If there are none ready, the call will block until one arrives or a deadline passes.

For its correct use in various scenarios, see the
                    use cases. In particular, sleep between calls to reduce load on the server.

The way this call picks events is very different from /api/events:

If you follow the link at events.updates.href in the results of GET /api, you will receive the first events that arrive after you make the call and that meet your other search criteria.

If you follow the updates link from the results of GET /api/events or GET
                  /api/events/updates, the link will contain a bookmark in the pos parameter that causes the server to return the first events that arrive or arrived after that bookmark (and that meet your search criteria, of course).

The response will contain an updates link back to the same API call with a new bookmark that will cause it to return the next page of results.

Each response will also contain a next link that will take you to the non-blocking version of the call at
                    /api/events.

However long you wait between calls, following a next or updates link will always return the first events that arrived after your previous call.

Do not code this URL into your application. Take it from events.updates.href in the results of GET /api, or from updates in the results of GET /api/events.

- If you follow the link at events.updates.href in the results of GET /api, you will receive the first events that arrive after you make the call and that meet your other search criteria.

- If you follow the updates link from the results of GET /api/events or GET
                  /api/events/updates, the link will contain a bookmark in the pos parameter that causes the server to return the first events that arrive or arrived after that bookmark (and that meet your search criteria, of course).

Sets the maximum number of events to return per page.

Sets the number of seconds to wait for an event, if none are ready when you make the call. If none arrive before this number of seconds pass the result set will be empty. If not specified, a default will apply.

Removes events that occurred before this time from the result set. It must be an ISO-8601 date or date-time with a timezone.

It is unlikely you will add this parameter to GET /api/events/updates. However it is a very useful parameter to
                        /api/events, and it will pass from there into the updates URL that that call returns. In that case it has no effect; you can leave it there.

This will not push the start time of the search into the past. As described above, the search will start at the time of the call or after the last event in a previous result set depending on the pos parameter. The after parameter reduces the results to those events that
                        arrived after the start time of the search and
                        occurred after the after timestamp. Note that an event's arrival time can be different from its occurrence time.

Removes events that occurred at or after this time from the result set. It must be an ISO-8601 date or date-time string with a timezone.

If no events arrive with an occurrence time earlier than this parameter, the call will eventually time out.

It is unlikely you will want this parameter in a call to GET /api/events/updates. It puts an end-date on the search, which is a very odd thing to do on a call intended to keep the caller up to date with events as they arrive. If you find yourself using it, you may wish to reconsider your approach.

If you only wish to receive events up to a point in history, use the before parameter on
                        /api/events, following its next block in a loop until you get an empty result. If you only wish to receive events up to a point in the future, use /api/events again but loop until you receive an event with a date beyond your stopping point.

Restricts events to those whose source item has this ID. Separate multiple IDs with commas. Use /api/items to search Command Centre's items.

Restricts events to those with this event type ID. Separate multiple IDs with commas.

Use /api/events/groups to see all of Command Centre's event types and groups. Event types names and IDs rarely change, but the ID is the more stable of the two. Therefore it is probably safer to use that API for reference then hard-code the event type IDs you find there into your application.

Restricts events to those with this event group ID. Separate multiple IDs with commas.

The documentation for /api/events/groups advises when to filter for event groups instead of event types.

Like event types, hard-coding the ID into your application is probably stabler (and definitely simpler) than searching for it at runtime.

Restricts events to those associated with the cardholder with this Command Centre ID. Separate multiple IDs with commas.

Example: cardholder=325

Restricts events to those in the division with this ID and its descendant divisions. Separate multiple IDs with commas.

Example: division=2,101

Sets the fields you want in your results.

This is the list of valid fields for 8.40, but it is different for older server versions, so check with the topic
                        on event field specifiers.

Restricts events to those with event IDs greater than this parameter.

INTERNAL USE ONLY. Retain the 'next' or 'updates' link in your application instead. This is how Command Centre tracks the events you have seen already. Do not set it yourself.

Success

The site does not have a RESTEvents licence.

Full details for an event. You could follow the href in the event summary to get here, but if you are running 8.20 or later you could just use the fields parameter to add the details field to the summary results for the same result.

The ID of the event.

Sets the fields you want in your results.

This is the list of valid fields for 8.40, but it is different for older server versions, so check with the topic
                        on event field specifiers.

Success

The site does not have a RESTEvents licence.

That is not the URL of an event, or it is but you do not have privileges to read events in its division.

Retrieves the list of event type groups and the event types within those groups. Useful for obtaining IDs to use in event filters. Command Centre ships with about 1000 event types divided into about 150 groups. Each event type is in one group.

A site may rename the 30 groups dedicated to external event types—that is, event types for the site's own use—and may create another 970 event types for them.

The results of this query vary with:

Event type identifiers do not often change between Command Centre versions, but the types in each group do. Therefore if you choose to use groups in filters rather than types you may find that your filter catches more or fewer event types after a Command Centre upgrade. That may be desirable if, for example, you are intested in a class of event and you want to grow with CC as it grows new features. If you want to catch all 'access denied's, for example. We frequently add new varieties of 'access denied'. If you do not want that, use types rather than groups.

Do not code this URL into your application. Take it from events.eventGroups.href in the results of GET /api.

- the server version,

- extra Gallagher software installed on the site, and

- changes to external event groups and types made by the customer.

Success

The site does not have the RESTEvents or RESTCreateEvents licence.

### Searching for items

### Status subscriptions

### Licensing

These methods let you find items for your search filters and events, and monitor their states. They return all items, including those that this API does not yet support in depth, and items added by customisations. However because they do not have deep knowledge of item types they can only give you the most basic fields.

Use the search methods when you are building a filter for an
                event search or a
                status subscription and need the ID of an item or an item type, or when you are
                creating an event and need an href to use as the event source.

To find an item, pass a substring of its name to the link at features.items.items.href in the results of a call to /api. If you are sure of its name, place the name inside " quotes, and it will use a full string match. Both types of search are case-insensitive.

To limit the search to items of a particular type, first get the ID of the type you are after using the link at features.items.itemTypes.href in the results of GET /api. Add that ID as the type parameter to the call above. You can specify multiple item types if, for example, you are interested in all the different kinds of doors.

For example, if you were after a list of divisions, following the instructions above on the current versions of Command Centre would produce the URL /api/items?type=15.

The
                item-specific APIs monitor only one item at a time, and are therefore not suitable for watching large collections. If you have CC version 8.30 or later, you should use the status subscription methods instead. We have tested subscriptions of 1000 items without noticing undue strain on the server. While the calls do not impose an upper bound on that we suggest keeping a watchful eye on the performance of the overall system if you go much higher.

The basic operations for monitoring item states is:

Get all the IDs of the items you wish to monitor. You can do that using this API's own
                    search with a name, division, or type parameter. If you're searching by division you'll need a division ID which (slightly recursively) is best found by using the same search function filtering for just divisions. If you're searching by type you'll need a
                    type ID.

POST to create a subscription. Your program should get the URL from items.updates.href in the results of GET /api.

Take the current state of your items from the results of that call. If that's all you need, terrific.

But if you want to monitor their state,
                    GET the next.href link that came in the results of the POST. The call will block until one of your monitored items changes state. When it returns, the results will be in the same format as the result of the POST, including the next link.

Loop.

Every REST licence enables the items controller: RESTEvents, RESTCreateEvents, RESTCardholders, RESTStatus, and RESTOverrides.

- Get all the IDs of the items you wish to monitor. You can do that using this API's own
                    search with a name, division, or type parameter. If you're searching by division you'll need a division ID which (slightly recursively) is best found by using the same search function filtering for just divisions. If you're searching by type you'll need a
                    type ID.

- POST to create a subscription. Your program should get the URL from items.updates.href in the results of GET /api.

- Take the current state of your items from the results of that call. If that's all you need, terrific.

- But if you want to monitor their state,
                    GET the next.href link that came in the results of the POST. The call will block until one of your monitored items changes state. When it returns, the results will be in the same format as the result of the POST, including the next link.

- Loop.

This returns a batch of items matching the applied filters. By default, each page will contain up to 1000 items although this can be changed by setting the top parameter in the request URL.

You will only receive items for which the REST operator has the necessary privilege. To view PDFs, for example, the operator must have the 'View Personal Data Definitions' privilege.

If more items are available, the response will contain a next link. Following that will return the next batch of items.

Items will be in ID order unless you change it with sort.

Do not code this URL into your application. Take it from items.items.href in the results of GET /api.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Only returns items that are in these divisions or their subdivisions. Use the divisions' short alphanumeric IDs, separateed by commas.

Only returns items that are of a type with this ID. In versions up to 8.30 you could only specify one, but in 8.40 and later this can be a comma-separated list.

Sets the maximum number of items to return per page.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using it you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success

The site does not have a REST licence.

This returns some basic fields for one item. It returns the same information as the
                    item search plus the item's division.

Added in 8.40.

The ID of the item.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using it you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

Success

That is not the href of an item. At least not one that your operator has the privilege to view.

Retrieves the list of all item types in the Command Centre system. There are about 200. This is useful for obtaining type IDs to use in item search filters.

Note that some item types have a blank name. These types are vestigial: disregard them.

Do not code this URL into your application. Take it from items.itemTypes.href in the results of GET /api.

Success

The site does not have a REST licence.

Creates a subscription to status changes. You POST a list of item IDs and the server returns the status flags of those items plus a link. When you GET that link some time later the server will return the items that changed state between the two calls.

If you do not GET the link within thirty seconds of the POST returning, the server will drop your subscription and free up the resources it had allocated to servicing it.

Your operator must have view privileges on every item in the subscription.

The subscription notices changes in state, not in configuration, so an operator modifying an item will not cause anything to come out of this API unless the change in configuration also causes a change in state.

Because this call returns the status of items, this call requires the RESTStatus licence.

Added in 8.30.

The body of the POST needs to contain a list of item IDs in an array called itemIds. Even though they look like small integers, these IDs are actually strings so don't forget the quotes.

Success

The server could not parse the request body. Check your JSON.

The operator does not have privilege to view the monitored items. This will change to 403 in a future version of Command Centre.

The site does not have the RESTStatus licence.

Collects status updates from a subscription created using the
                    /api/items/updates POST.

This is a long poll, so if there are no updates waiting when you make the call it will block until some arrive or a timeout passes (about 50 seconds).

If you receive a 404 from this call it means that too much time has passed since the server sent the link, or the server has restarted. In either case the server will have dropped your subscription. You will need to create a new one with a fresh POST.

Therefore your loop can be:

...plus the necessary exception handling, of course.

Added in 8.30.

Note that the first time you make this GET request it will return all the activity that came back from the POST. There are other cases where the GET might return data you have seen already (when an override is sent to an item, or some aspect of an item changes that your operator does not have the privilege to view, for example). These two behaviours are not harmful, but also not particularly helpful, so future versions may differ.

- Create a subscription with a
                      POST.

- Process the statuses in the results, if there are any.

- Wait a second or two to avoid tight loops.

- GET the link from the results. It may take up to a minute to respond.

- If 404, go to 1.

- Go to 2.

Identifies your subscription and your position in the change list. You do not need to set this parameter: it will be in the link that the server sends back to you.

Success

The site does not have a REST licence.

The subscription does not exist, which probably means you waited too long between calls.

An array of alarm summaries, and either a 'next' or an 'updates' link you should follow to retrieve more alarms.

An array of alarm summaries.

The link to the next page of alarms. Missing if you have retrieved all the current alarms.

The link to follow to long-poll for alarm changes. Missing if you have not yet retrieved all the current alarms.

**alarms:


Alarm summary:** An array of alarm summaries.

**Alarm summary:** The link to the next page of alarms. Missing if you have retrieved all the current alarms.

**next:
object:** The link to follow to long-poll for alarm changes. Missing if you have not yet retrieved all the current alarms.

An array of summaries of alarms created or modified since the previous call.

Follow this link to perform another long poll.

**updates:


Alarm summary:** An array of summaries of alarms created or modified since the previous call.

**Alarm summary:** Follow this link to perform another long poll.

/api/alarms returns an array of these, and /api/alarms/{id} returns one with more fields.

A link to the details of this alarm.

An alphanumeric identifier for this alarm, unique to the server.

The time the alarm occurred.

The alarm's message.

The ID and href are new to 8.10.

The alphanumeric ID of the alarm's source item.

The name of the alarm's source item at the time of the event.

Link to the source item.

The name of this alarm's event type.

ID and name of the event's or alarm's type. There is a long list of them at
                        /events/groups.

Unlike the type field, this has the same format in an event as it does in an alarm.

Added in 8.90. Because it is a new field, it does not appear by default. Ask for it using the fields parameter.

The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with
                              /events.

The name of the event type.

Numeric priority. 9 is critical and 0 is not an event.

The state of the alarm. Alarms start at "unacknowledged".

Clients cannot process active alarms.

The division entity representing the division of the alarm. GET the href for full details.

A block containing a link ("href") to the details page of this alarm's event. Useful if you want an alarm's related items.

This mirrors an event's alarm field, which comes the other way.

Added in 8.90. Not returned by default - you need to ask for it using the fields query parameter.

Link to the event page for this alarm. GET it for the fields found on an event that are not on an alarm.

Preset alarm notes to use for this specific alarm. Missing if the alarm does not have specific notes; in this case the client should fall back to the server defaults.

POST an alarm update request JSON object to the href to indicate the operator has viewed the alarm.

POST an alarm update request JSON object to the href to place an arbitrary alarm note against the alarm.

POST an alarm update request JSON object to the href to acknowledge an alarm. Missing if the system has mandatory alarm notes on for this alarm, or if the alarm is already acknowledged.

POST an alarm update request JSON object to the href to acknowledge an alarm and place an alarm note against it. Missing if the alarm is already acknowledged.

POST an alarm update request JSON object to the href to process an alarm. Missing if the system has mandatory alarm notes on for this alarm. Missing if the alarm cannot be processed (if it is active, for example).

POST an alarm update request JSON object to the href to process an alarm and place an alarm note against it. Missing if the alarm cannot be processed (if it is active, for example).

POST an alarm update request JSON object to this href to process an active alarm. Missing if the alarm is not active (in which case you will have process and processWithComment links instead).

**href:
string
(url):** A link to the details of this alarm.

**id:
string:** An alphanumeric identifier for this alarm, unique to the server.

**time:
string:** The time the alarm occurred.

**message:
string:** The alarm's message.

**source:
object:** The ID and href are new to 8.10.

**id:
string



The alphanumeric ID of the alarm's source item.


name:
string



The name of the alarm's source item at the time of the event.


href:
string
(url)



Link to the source item.:** The alphanumeric ID of the alarm's source item.

**id:
string:** The name of the alarm's source item at the time of the event.

**name:
string:** Link to the source item.

**href:
string
(url):** The name of this alarm's event type.

**type:
string:** ID and name of the event's or alarm's type. There is a long list of them at
                        /events/groups.
Unlike the type field, this has the same format in an event as it does in an alarm.
Added in 8.90. Because it is a new field, it does not appear by default. Ask for it using the fields parameter.

**eventType:
object:** The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with
                              /events.

**id:
string



The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with
                              /events.


name:
string



The name of the event type.:** The name of the event type.

**id:
string:** Numeric priority. 9 is critical and 0 is not an event.

**name:
string:** The state of the alarm. Alarms start at "unacknowledged".

**priority:
integer
0 ≤ x ≤ 9:** Clients cannot process active alarms.

**state:
string

unacknowledged,
                        acknowledged,
                        processed:** The division entity representing the division of the alarm. GET the href for full details.

**active:
boolean:** A block containing a link ("href") to the details page of this alarm's event. Useful if you want an alarm's related items.
This mirrors an event's alarm field, which comes the other way.
Added in 8.90. Not returned by default - you need to ask for it using the fields query parameter.

**division:
object:** Link to the event page for this alarm. GET it for the fields found on an event that are not on an alarm.

**href:
string
(url):** Preset alarm notes to use for this specific alarm. Missing if the alarm does not have specific notes; in this case the client should fall back to the server defaults.

**href:
string
(url):** POST an alarm update request JSON object to the href to indicate the operator has viewed the alarm.

**event:
object:** POST an alarm update request JSON object to the href to place an arbitrary alarm note against the alarm.

**href:
string
(url)



Link to the event page for this alarm. GET it for the fields found on an event that are not on an alarm.:** POST an alarm update request JSON object to the href to acknowledge an alarm. Missing if the system has mandatory alarm notes on for this alarm, or if the alarm is already acknowledged.

**href:
string
(url):** POST an alarm update request JSON object to the href to acknowledge an alarm and place an alarm note against it. Missing if the alarm is already acknowledged.

**notePresets:
string[]:** POST an alarm update request JSON object to the href to process an alarm. Missing if the system has mandatory alarm notes on for this alarm. Missing if the alarm cannot be processed (if it is active, for example).

**string:** POST an alarm update request JSON object to the href to process an alarm and place an alarm note against it. Missing if the alarm cannot be processed (if it is active, for example).

**view:
object:** POST an alarm update request JSON object to this href to process an active alarm. Missing if the alarm is not active (in which case you will have process and processWithComment links instead).

**id:
string:** The alphanumeric ID of the alarm's source item.

**name:
string:** The name of the alarm's source item at the time of the event.

**href:
string
(url):** Link to the source item.

**id:
string:** The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with
                              /events.

**name:
string:** The name of the event type.

**href:
string
(url):** Link to the event page for this alarm. GET it for the fields found on an event that are not on an alarm.

/api/alarms/{id} returns one of these. It contains everything from the alarm summary results, plus some extra fields that are too expensive to compute and return for large result sets.

The full alarm details text. This may be up to 2048 UTF-8 characters, each of which could (theoretically) be four bytes long.

An array of alarm history entries. Missing if there is no history.

GET this link to retrieve the alarm instruction body inside an HTML document. Missing if there is no alarm instruction for this alarm.

The cardholder entity associated with this alarm. GET the href for full details. Missing if this alarm does not have an associated cardholder.

In versions up to and including 8.10 this is the current name of the cardholder. In 8.20 it is the name of the cardholder at the time of the event.

The current value of the firstName field of this cardholder. Added in 8.20.

The current value of the lastName field of this cardholder. Added in 8.20.

**details:
string:** The full alarm details text. This may be up to 2048 UTF-8 characters, each of which could (theoretically) be four bytes long.

**history:


Alarm history entry:** An array of alarm history entries. Missing if there is no history.

**Alarm history entry:** GET this link to retrieve the alarm instruction body inside an HTML document. Missing if there is no alarm instruction for this alarm.

**instruction:
object:** The cardholder entity associated with this alarm. GET the href for full details. Missing if this alarm does not have an associated cardholder.

**href:
string
(url):** In versions up to and including 8.10 this is the current name of the cardholder. In 8.20 it is the name of the cardholder at the time of the event.

**href:
string
(url):** The current value of the firstName field of this cardholder. Added in 8.20.

**cardholder:
object:** The current value of the lastName field of this cardholder. Added in 8.20.

**href:
string
(url):** In versions up to and including 8.10 this is the current name of the cardholder. In 8.20 it is the name of the cardholder at the time of the event.

**name:
string:** The current value of the firstName field of this cardholder. Added in 8.20.

**firstName:
string:** The current value of the lastName field of this cardholder. Added in 8.20.

The time the history entry was added.

The type of the history entry.

The added comment, or a textual description of some occurrence related to the alarm.

The operator that created the history event.

**time:
string:** The time the history entry was added.

**action:
string

legacy,
                        comment,
                        acknowledge,
                        process,
                        acknowledgeActive,
                        escalated,
                        viewed:** The type of the history entry.

**comment:
string:** The added comment, or a textual description of some occurrence related to the alarm.

**operator:
object:** The operator that created the history event.

Optional for some methods that update alarms. Contains a comment placed by the operator.

**comment:
string:** Optional for some methods that update alarms. Contains a comment placed by the operator.

/api/events and /api/events/updates return this structure.

A list of event summaries.

Follow this link to make a non-blocking call to collect the previous page of events (in order of arrival). It will return an empty page if you have already received the first event in the database.

Follow this link to make a non-blocking call to collect the next page of events, moving forward in arrival time. It will return an empty page if no more events are available.

This is a link to an
                        updates call. Follow it to make a blocking call to collect more events. If there are none, the call will block until one arrives or the call times out. This link will contain your column select, pagination, and filtering parameters (fields, top, after, before, source, type, etc.) but it drops deadline. You need to add that to each call.

**events:


Event summary:** A list of event summaries.

**Event summary:** Follow this link to make a non-blocking call to collect the previous page of events (in order of arrival). It will return an empty page if you have already received the first event in the database.

**previous:
object:** Follow this link to make a non-blocking call to collect the next page of events, moving forward in arrival time. It will return an empty page if no more events are available.

**href:
string
(url):** This is a link to an
                        updates call. Follow it to make a blocking call to collect more events. If there are none, the call will block until one arrives or the call times out. This link will contain your column select, pagination, and filtering parameters (fields, top, after, before, source, type, etc.) but it drops deadline. You need to add that to each call.

/api/events and /api/events/updates return an array of these, and /api/events/{id} returns one with more fields.

The message and event type in this example indicate an operator attempting to log in with an incorrect password, but for the sake of illustration the example also contains references to items that would never appear on such an event. The card, cardholder, and access zones, for example.

A link to this event's details.

An alphanumeric identifier for this event, unique to the server.

The host name of this event's origin server, if it was aggregated from a remote host. Absent for local events.

Note that this is not the descriptive name of the remote server's item, but the host name used for address resolution.

New to 8.40.

The time the event occurred.

The event's message.

If an event arrives with the same essential properties as a previous event, the server will start counting them. Each event is still individually addressable and will appear in the API as normal, but the first in the group will also have this property. It only appears on the first, and it does not appear if the event is a singleton.

If present on an event's detail or alarm page, there will also be a lastOccurrenceTime.

Outside of the alarm block, which contains an alarm's state, this is the only field on an event that can change.

Numeric priority. 9 is critical and 0 is not an event.

If an event is also an alarm, this object will contain its state and a link to its details page in the alarms controller.

Only the first event in a group (see the occurrences field) can become an alarm.

Alarms start unacknowledged. Acknowledging or processing them changes that.

Link to the alarm entity corresponding to the event. GET the href for full alarm details.

The href and name of the operator behind this event. This will appear when an operator has modified an item. New in v8.00.

The item he or she modified will appear in the modifiedItem block (added in 8.40).

Link to the operator who caused this event by editing an item. New in 8.00.

The name the operator held at the time. New in 8.40.

ID and name of the source of the event, as recorded at the time of the event.

The alphanumeric ID of the event source item. Search for events with the same source as this one with source=321 in the query parameters.

This could be different from the current name of the source item.

Link to the source item. New in 8.00.

ID and name of the event group this event belongs to. Do not confuse this with an access group or operator group: this is the event type group to which the event's type belongs. There are about 150 and you can list them at
                        /events/groups.

The alphanumeric ID of the event group. Search for events of the same rough category as this one with type=35 in the query parameters.

The name of the event group.

ID and name of the event's type. There is a long list of them at
                        /events/groups.

The alphanumeric ID of the event type. To search for events of the same type as this example, put type=23035 in the query parameters of
                              /events.

The name of the event type.

ID and name of the event's or alarm's type. There is a long list of them at
                        /events/groups.

Unlike the type field, this has the same format in an event as it does in an alarm.

Added in 8.90. Because it is a new field, it does not appear by default. Ask for it using the fields parameter.

The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with
                              /events.

The name of the event type.

ID, name, and href of the event's division (which is the division of the event's source item, for most event types).

The alphanumeric ID of the event's division. Search for other events from items in the same division as this example by putting division=2 in the query parameters.

The link to the division item.

The division's name. Added in 8.40.

Summary information about an event's cardholder, if there is one. This will be the cardholder who badged their card at a door in an access event, or the cardholder an operator modified in an operator event. Search for other events related to this example's cardholder with cardholder=325 in the query parameters.

Link to the cardholder entity representing the cardholder of the event. GET the href for full details.

The alphanumeric ID of the cardholder associated with this event.

In versions up to and including 8.10 this is the current name of the cardholder. In 8.20 it is the name of the cardholder at the time of the event.

The current value of the firstName field of this cardholder. Added in 8.20.

The current value of the lastName field of this cardholder. Added in 8.20.

The name and href of the entry access zone related to the event. In the case of card events, it is the zone into which a cardholder was attempting to gain access.

That is true for successful entries, successful exits, and access denials. Regardless of whether the cardholder badged at the entry or exit reader, this field refers to the zone that he or she attempted to access.

For example, for 'Card entry granted' events this field will contain the door's entry zone, but for 'Card exit granted' events this field will contain the door's
                        exit zone.

Link to the access zone entity representing the entry access zone related to this event. GET the href for the access zone's full details. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').

The current name of the access zone. Expect a future version of Command Centre to change this to change to the name of the access zone at the time of the event.

Deprecated.

The name and href of the exit access zone related to the event. In card events, it is the zone from which a cardholder was attempting to leave, if the door had an exit zone configured (many do not).

That is true for successful entries, successful exits, and access denials. Regardless of whether the cardholder badged at the entry or exit reader, this field refers to the zone that he or she attempted to leave.

For example, for 'Card entry granted' events this field will contain the door's exit zone, if there was one, but for 'Card exit granted' events this field will contain the door's
                        entry zone.

Link to the access zone entity representing the exit access zone related to this event. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').

The current name of the exit access zone. Expect a future version of Command Centre to change this to change to the name of the exit access zone at the time of the event.

Deprecated.

The name and href of the door related to the event. These are not as common as you may think, because when a door is relevant (to card events, for example) it is usually the event's source. New in 8.10.

Link to the entity representing the door related to this event. This will be missing if the server lacks the RESTStatus licence or your operator lacks the necessary privileges (such as 'View Site').

The name the door had when the event occurred.

The href of the access group that a cardholder just gained or lost in a 'Membership Activated' or 'Membership Expired' event. Those happen when a group membership's 'from' or 'until' time passes.

DEPRECATED for the events generated when an operator creates, modifies, or deletes an access group. Use the 'modifiedItem' field instead (new in 8.40).

Link to the access group related to this event.

Details of the card associated with the event.

Versions prior to 8.60 returned this block and a card number of zero for all access events, even if they did not involve a credential (after a person entered their user code at a keypad, for example). Version 8.60 does not return this block for such events.

The card's facility code at the time of the event expressed as one letter followed by up to five digits.

The card's number at the time of the event. Despite the name it may not necessarily be an actual number; mobile card numbers are arbitrary strings, for example.

Note that card numbers are not guaranteed unique. The combination of facility code, issue level, and card number will be unique for card types that have a facility code and issue level.

Before 8.60 this number was a signed 32-bit integer, so numeric card numbers greater than 2^31 came out negative and mobile card numbers were arbitrary. In 8.60 numeric card numbers are positive and mobile card numbers are their ID strings, which operators can set to anything, and default to GUIDs.

The issue level of the card at the time.

The href and type of the item that an operator created, changed, or deleted, for those kinds of events.

New in 8.40.

Link to the item that this event modified.

A short alphanum identifying the item's type.

A human-readable name of the item's type, suitable for display.

This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.

The URL to the search that will return the page of events following this one. It will include your search filters and pagination parameters.

This link is intended for integrations that take great gulps of events and send them to a downstream system. If it suffers a problem in the middle of a result set, it needs to record where in the event trail it got up to so that when it restarts it will not miss or duplicate events.

The next link on the last event in a result set will be the same as the next link outside the results.

New to 8.70.

The URL to the search that will return the page of events preceding this one. It will include your search filters and pagination parameters.

The previous link on the first event in a result set will be the same as the previous link outside the results, since they both indicate the latest event that preceded the result set.

New to 8.70.

The URL to the
                        updates call that will return the page of events following this one, or wait for one to arrive if there are none. It will include your search filters and pagination parameters.

The updates link on the last event in a result set will be the same as the updates link outside the results, since they both indicate the event that will be at the top of the next result set.

New to 8.70.

**href:
string
(url):** A link to this event's details.

**id:
string:** An alphanumeric identifier for this event, unique to the server.

**serverDisplayName:
string:** The host name of this event's origin server, if it was aggregated from a remote host. Absent for local events.
Note that this is not the descriptive name of the remote server's item, but the host name used for address resolution.
New to 8.40.

**time:
string
(date-time):** The time the event occurred.

**message:
string:** The event's message.

**occurrences:
integer
x ≥ 2:** If an event arrives with the same essential properties as a previous event, the server will start counting them. Each event is still individually addressable and will appear in the API as normal, but the first in the group will also have this property. It only appears on the first, and it does not appear if the event is a singleton.
If present on an event's detail or alarm page, there will also be a lastOccurrenceTime.
Outside of the alarm block, which contains an alarm's state, this is the only field on an event that can change.

**priority:
integer
0 ≤ x ≤ 9:** Numeric priority. 9 is critical and 0 is not an event.

**alarm:
object:** If an event is also an alarm, this object will contain its state and a link to its details page in the alarms controller.
Only the first event in a group (see the occurrences field) can become an alarm.

**state:
string

unacknowledged,
                              acknowledged,
                              processed




Alarms start unacknowledged. Acknowledging or processing them changes that.


href:
string
(url)



Link to the alarm entity corresponding to the event. GET the href for full alarm details.:** Alarms start unacknowledged. Acknowledging or processing them changes that.

**state:
string

unacknowledged,
                              acknowledged,
                              processed:** Link to the alarm entity corresponding to the event. GET the href for full alarm details.

**href:
string
(url):** The href and name of the operator behind this event. This will appear when an operator has modified an item. New in v8.00.
The item he or she modified will appear in the modifiedItem block (added in 8.40).

**operator:
object:** Link to the operator who caused this event by editing an item. New in 8.00.

**href:
string
(url)



Link to the operator who caused this event by editing an item. New in 8.00.


name:
string



The name the operator held at the time. New in 8.40.:** The name the operator held at the time. New in 8.40.

**href:
string
(url):** ID and name of the source of the event, as recorded at the time of the event.

**name:
string:** The alphanumeric ID of the event source item. Search for events with the same source as this one with source=321 in the query parameters.

**source:
object:** This could be different from the current name of the source item.

**id:
string



The alphanumeric ID of the event source item. Search for events with the same source as this one with source=321 in the query parameters.


name:
string



This could be different from the current name of the source item.


href:
string
(url)



Link to the source item. New in 8.00.:** Link to the source item. New in 8.00.

**id:
string:** ID and name of the event group this event belongs to. Do not confuse this with an access group or operator group: this is the event type group to which the event's type belongs. There are about 150 and you can list them at
                        /events/groups.

**name:
string:** The alphanumeric ID of the event group. Search for events of the same rough category as this one with type=35 in the query parameters.

**href:
string
(url):** The name of the event group.

**group:
object:** ID and name of the event's type. There is a long list of them at
                        /events/groups.

**id:
string



The alphanumeric ID of the event group. Search for events of the same rough category as this one with type=35 in the query parameters.


name:
string



The name of the event group.:** The alphanumeric ID of the event type. To search for events of the same type as this example, put type=23035 in the query parameters of
                              /events.

**id:
string:** The name of the event type.

**name:
string:** ID and name of the event's or alarm's type. There is a long list of them at
                        /events/groups.
Unlike the type field, this has the same format in an event as it does in an alarm.
Added in 8.90. Because it is a new field, it does not appear by default. Ask for it using the fields parameter.

**type:
object:** The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with
                              /events.

**id:
string



The alphanumeric ID of the event type. To search for events of the same type as this example, put type=23035 in the query parameters of
                              /events.


name:
string



The name of the event type.:** The name of the event type.

**id:
string:** ID, name, and href of the event's division (which is the division of the event's source item, for most event types).

**name:
string:** The alphanumeric ID of the event's division. Search for other events from items in the same division as this example by putting division=2 in the query parameters.

**eventType:
object:** The link to the division item.

**id:
string



The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with
                              /events.


name:
string



The name of the event type.:** The division's name. Added in 8.40.

**id:
string:** Summary information about an event's cardholder, if there is one. This will be the cardholder who badged their card at a door in an access event, or the cardholder an operator modified in an operator event. Search for other events related to this example's cardholder with cardholder=325 in the query parameters.

**name:
string:** Link to the cardholder entity representing the cardholder of the event. GET the href for full details.

**division:
object:** The alphanumeric ID of the cardholder associated with this event.

**id:
string



The alphanumeric ID of the event's division. Search for other events from items in the same division as this example by putting division=2 in the query parameters.


href:
string
(url)



The link to the division item.


name:
string



The division's name. Added in 8.40.:** In versions up to and including 8.10 this is the current name of the cardholder. In 8.20 it is the name of the cardholder at the time of the event.

**id:
string:** The current value of the firstName field of this cardholder. Added in 8.20.

**href:
string
(url):** The current value of the lastName field of this cardholder. Added in 8.20.

**name:
string:** The name and href of the entry access zone related to the event. In the case of card events, it is the zone into which a cardholder was attempting to gain access.
That is true for successful entries, successful exits, and access denials. Regardless of whether the cardholder badged at the entry or exit reader, this field refers to the zone that he or she attempted to access.
For example, for 'Card entry granted' events this field will contain the door's entry zone, but for 'Card exit granted' events this field will contain the door's
                        exit zone.

**cardholder:
object:** Link to the access zone entity representing the entry access zone related to this event. GET the href for the access zone's full details. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').

**href:
string
(url)



Link to the cardholder entity representing the cardholder of the event. GET the href for full details.


id:
string



The alphanumeric ID of the cardholder associated with this event.


name:
string



In versions up to and including 8.10 this is the current name of the cardholder. In 8.20 it is the name of the cardholder at the time of the event.


firstName:
string



The current value of the firstName field of this cardholder. Added in 8.20.


lastName:
string



The current value of the lastName field of this cardholder. Added in 8.20.:** The current name of the access zone. Expect a future version of Command Centre to change this to change to the name of the access zone at the time of the event.

**href:
string
(url):** Deprecated.

**id:
string:** The name and href of the exit access zone related to the event. In card events, it is the zone from which a cardholder was attempting to leave, if the door had an exit zone configured (many do not).
That is true for successful entries, successful exits, and access denials. Regardless of whether the cardholder badged at the entry or exit reader, this field refers to the zone that he or she attempted to leave.
For example, for 'Card entry granted' events this field will contain the door's exit zone, if there was one, but for 'Card exit granted' events this field will contain the door's
                        entry zone.

**name:
string:** Link to the access zone entity representing the exit access zone related to this event. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').

**firstName:
string:** The current name of the exit access zone. Expect a future version of Command Centre to change this to change to the name of the exit access zone at the time of the event.

**lastName:
string:** Deprecated.

**entryAccessZone:
object:** The name and href of the door related to the event. These are not as common as you may think, because when a door is relevant (to card events, for example) it is usually the event's source. New in 8.10.

**href:
string
(url)



Link to the access zone entity representing the entry access zone related to this event. GET the href for the access zone's full details. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').


name:
string



The current name of the access zone. Expect a future version of Command Centre to change this to change to the name of the access zone at the time of the event.


id:
string



Deprecated.:** Link to the entity representing the door related to this event. This will be missing if the server lacks the RESTStatus licence or your operator lacks the necessary privileges (such as 'View Site').

**href:
string
(url):** The name the door had when the event occurred.

**name:
string:** The href of the access group that a cardholder just gained or lost in a 'Membership Activated' or 'Membership Expired' event. Those happen when a group membership's 'from' or 'until' time passes.
DEPRECATED for the events generated when an operator creates, modifies, or deletes an access group. Use the 'modifiedItem' field instead (new in 8.40).

**id:
string:** Link to the access group related to this event.

**exitAccessZone:
object:** Details of the card associated with the event.
Versions prior to 8.60 returned this block and a card number of zero for all access events, even if they did not involve a credential (after a person entered their user code at a keypad, for example). Version 8.60 does not return this block for such events.

**href:
string
(url)



Link to the access zone entity representing the exit access zone related to this event. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').


name:
string



The current name of the exit access zone. Expect a future version of Command Centre to change this to change to the name of the exit access zone at the time of the event.


id:
string



Deprecated.:** The card's facility code at the time of the event expressed as one letter followed by up to five digits.

**href:
string
(url):** The card's number at the time of the event. Despite the name it may not necessarily be an actual number; mobile card numbers are arbitrary strings, for example.
Note that card numbers are not guaranteed unique. The combination of facility code, issue level, and card number will be unique for card types that have a facility code and issue level.
Before 8.60 this number was a signed 32-bit integer, so numeric card numbers greater than 2^31 came out negative and mobile card numbers were arbitrary. In 8.60 numeric card numbers are positive and mobile card numbers are their ID strings, which operators can set to anything, and default to GUIDs.

**name:
string:** The issue level of the card at the time.

**id:
string:** The href and type of the item that an operator created, changed, or deleted, for those kinds of events.
New in 8.40.

**door:
object:** Link to the item that this event modified.

**href:
string
(url)



Link to the entity representing the door related to this event. This will be missing if the server lacks the RESTStatus licence or your operator lacks the necessary privileges (such as 'View Site').


name:
string



The name the door had when the event occurred.:** A short alphanum identifying the item's type.

**href:
string
(url):** A human-readable name of the item's type, suitable for display.
This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.

**name:
string:** The URL to the search that will return the page of events following this one. It will include your search filters and pagination parameters.
This link is intended for integrations that take great gulps of events and send them to a downstream system. If it suffers a problem in the middle of a result set, it needs to record where in the event trail it got up to so that when it restarts it will not miss or duplicate events.
The next link on the last event in a result set will be the same as the next link outside the results.
New to 8.70.

**accessGroup:
object:** The URL to the search that will return the page of events preceding this one. It will include your search filters and pagination parameters.
The previous link on the first event in a result set will be the same as the previous link outside the results, since they both indicate the latest event that preceded the result set.
New to 8.70.

**href:
string
(url)



Link to the access group related to this event.:** The URL to the
                        updates call that will return the page of events following this one, or wait for one to arrive if there are none. It will include your search filters and pagination parameters.
The updates link on the last event in a result set will be the same as the updates link outside the results, since they both indicate the event that will be at the top of the next result set.
New to 8.70.

**state:
string

unacknowledged,
                              acknowledged,
                              processed:** Alarms start unacknowledged. Acknowledging or processing them changes that.

**href:
string
(url):** Link to the alarm entity corresponding to the event. GET the href for full alarm details.

**href:
string
(url):** Link to the operator who caused this event by editing an item. New in 8.00.

**name:
string:** The name the operator held at the time. New in 8.40.

**id:
string:** The alphanumeric ID of the event source item. Search for events with the same source as this one with source=321 in the query parameters.

**name:
string:** This could be different from the current name of the source item.

**href:
string
(url):** Link to the source item. New in 8.00.

**id:
string:** The alphanumeric ID of the event group. Search for events of the same rough category as this one with type=35 in the query parameters.

**name:
string:** The name of the event group.

**id:
string:** The alphanumeric ID of the event type. To search for events of the same type as this example, put type=23035 in the query parameters of
                              /events.

**name:
string:** The name of the event type.

**id:
string:** The alphanumeric ID of the event type. You can use this ID in the type parameter when searching for events with
                              /events.

**name:
string:** The name of the event type.

**id:
string:** The alphanumeric ID of the event's division. Search for other events from items in the same division as this example by putting division=2 in the query parameters.

**href:
string
(url):** The link to the division item.

**name:
string:** The division's name. Added in 8.40.

**href:
string
(url):** Link to the cardholder entity representing the cardholder of the event. GET the href for full details.

**id:
string:** The alphanumeric ID of the cardholder associated with this event.

**name:
string:** In versions up to and including 8.10 this is the current name of the cardholder. In 8.20 it is the name of the cardholder at the time of the event.

**firstName:
string:** The current value of the firstName field of this cardholder. Added in 8.20.

**lastName:
string:** The current value of the lastName field of this cardholder. Added in 8.20.

**href:
string
(url):** Link to the access zone entity representing the entry access zone related to this event. GET the href for the access zone's full details. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').

**name:
string:** The current name of the access zone. Expect a future version of Command Centre to change this to change to the name of the access zone at the time of the event.

**id:
string:** Deprecated.

**href:
string
(url):** Link to the access zone entity representing the exit access zone related to this event. This will be missing if the server lacks the RESTStatus licence, or your operator lacks the necessary privileges (such as 'View Site').

**name:
string:** The current name of the exit access zone. Expect a future version of Command Centre to change this to change to the name of the exit access zone at the time of the event.

**id:
string:** Deprecated.

**href:
string
(url):** Link to the entity representing the door related to this event. This will be missing if the server lacks the RESTStatus licence or your operator lacks the necessary privileges (such as 'View Site').

**name:
string:** The name the door had when the event occurred.

**href:
string
(url):** Link to the access group related to this event.

**facilityCode:
string:** The card's facility code at the time of the event expressed as one letter followed by up to five digits.

**number:
string:** The card's number at the time of the event. Despite the name it may not necessarily be an actual number; mobile card numbers are arbitrary strings, for example.
Note that card numbers are not guaranteed unique. The combination of facility code, issue level, and card number will be unique for card types that have a facility code and issue level.
Before 8.60 this number was a signed 32-bit integer, so numeric card numbers greater than 2^31 came out negative and mobile card numbers were arbitrary. In 8.60 numeric card numbers are positive and mobile card numbers are their ID strings, which operators can set to anything, and default to GUIDs.

**issueLevel:
integer:** The issue level of the card at the time.

**href:
string
(url):** Link to the item that this event modified.

**type:
object:** A short alphanum identifying the item's type.

**id:
string



A short alphanum identifying the item's type.


name:
string



A human-readable name of the item's type, suitable for display.
This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.:** A human-readable name of the item's type, suitable for display.
This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.

**id:
string:** A short alphanum identifying the item's type.

**name:
string:** A human-readable name of the item's type, suitable for display.
This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.

/api/events/{id} returns one of these. It contains everything from the event summary results, plus some extra fields that are too expensive to compute and return for large result sets.

Like the example in the
                    summary, this is somewhat forced: no actual Command Centre event will contain all of these fields, unless you create one yourself.

When the event has occurred multiple times due to flooding, this will show the time it occurred most recently. It only appears with occurrences, and you will only see it on the first event in a group.

The full alarm details text. This may be up to 2048 UTF-8 characters, each of which could (theoretically) be four bytes long.

This block gathers together information that is contained in about 80 different event types that contain a cardholder's location, presenting it in a consistent way.

The feature allows a client that is interested in movements to extract what it needs without needing to know what event types to look for or how they represent these three fields:

This field will only be in a result if you ask for it. Use the fields query parameter to do that.

This feature is coming to 9.00. While it is still in development
                            this documentation remains subject to change.

This enum divides location events into three types:

moved means a cardholder moved through a door from one zone to another. Whether they used a plastic card, a mobile credential, biometrics, a QR code, a vehicle plate, or something implemented by a third-party integration, provided they were granted access, this field's value will be moved.

denied means the cardholder requested access at a door but failed the access check.

observed means they did something that indicated their location, such as logging in to a workstation or an alarms terminal.

A block describing the cardholder whose location is recorded by the event.

The cardholder's name at the time of the event.

The cardholder item's href. This will only be present if your operator has the right to view the cardholder.

The href and name of the access zone or reception that the cardholder was in before this event occurred.

It is only useful (and present) for 'moved'-type events, which happen when a cardholder changes location. For 'observed' and 'denied'-type events, which happen when a cardholder does not move, afterLocation shows where they were.

The name was correct at the time of the event. The href will be missing if your operator does not have the right to view it.

The href and name of the access zone or reception that the cardholder was in after the event. For 'observed' and 'denied'-type events, this is also where they started.

If they moved "outside the system" (entering through a door with no entry zone, or exiting through a door with no exit zone), there will be no href. The href will also be missing if your operator does not have the right to view it.

The name was correct at the time of the event.

- The cardholder whose location the event contains,

- the access zone that they started in before the event, and

- the access zone or reception they ended up in as a result of the event.

**lastOccurrenceTime:
string
(date-time):** When the event has occurred multiple times due to flooding, this will show the time it occurred most recently. It only appears with occurrences, and you will only see it on the first event in a group.

**details:
string:** The full alarm details text. This may be up to 2048 UTF-8 characters, each of which could (theoretically) be four bytes long.

**location:
object:** This block gathers together information that is contained in about 80 different event types that contain a cardholder's location, presenting it in a consistent way.
The feature allows a client that is interested in movements to extract what it needs without needing to know what event types to look for or how they represent these three fields:

The cardholder whose location the event contains,
the access zone that they started in before the event, and
the access zone or reception they ended up in as a result of the event.

This field will only be in a result if you ask for it. Use the fields query parameter to do that.
This feature is coming to 9.00. While it is still in development
                            this documentation remains subject to change.

**type:
string

moved,
                                  observed,
                                  denied




This enum divides location events into three types:
moved means a cardholder moved through a door from one zone to another. Whether they used a plastic card, a mobile credential, biometrics, a QR code, a vehicle plate, or something implemented by a third-party integration, provided they were granted access, this field's value will be moved.
denied means the cardholder requested access at a door but failed the access check.
observed means they did something that indicated their location, such as logging in to a workstation or an alarms terminal.


cardholder:
object



A block describing the cardholder whose location is recorded by the event.





name:
string



The cardholder's name at the time of the event.


href:
string
(url)



The cardholder item's href. This will only be present if your operator has the right to view the cardholder.





beforeLocation:
object



The href and name of the access zone or reception that the cardholder was in before this event occurred.
It is only useful (and present) for 'moved'-type events, which happen when a cardholder changes location. For 'observed' and 'denied'-type events, which happen when a cardholder does not move, afterLocation shows where they were.
The name was correct at the time of the event. The href will be missing if your operator does not have the right to view it.


afterLocation:
object



The href and name of the access zone or reception that the cardholder was in after the event. For 'observed' and 'denied'-type events, this is also where they started.
If they moved "outside the system" (entering through a door with no entry zone, or exiting through a door with no exit zone), there will be no href. The href will also be missing if your operator does not have the right to view it.
The name was correct at the time of the event.:** This enum divides location events into three types:
moved means a cardholder moved through a door from one zone to another. Whether they used a plastic card, a mobile credential, biometrics, a QR code, a vehicle plate, or something implemented by a third-party integration, provided they were granted access, this field's value will be moved.
denied means the cardholder requested access at a door but failed the access check.
observed means they did something that indicated their location, such as logging in to a workstation or an alarms terminal.

**type:
string

moved,
                                  observed,
                                  denied:** A block describing the cardholder whose location is recorded by the event.

**cardholder:
object:** The cardholder's name at the time of the event.

**name:
string



The cardholder's name at the time of the event.


href:
string
(url)



The cardholder item's href. This will only be present if your operator has the right to view the cardholder.:** The cardholder item's href. This will only be present if your operator has the right to view the cardholder.

**name:
string:** The href and name of the access zone or reception that the cardholder was in before this event occurred.
It is only useful (and present) for 'moved'-type events, which happen when a cardholder changes location. For 'observed' and 'denied'-type events, which happen when a cardholder does not move, afterLocation shows where they were.
The name was correct at the time of the event. The href will be missing if your operator does not have the right to view it.

**href:
string
(url):** The href and name of the access zone or reception that the cardholder was in after the event. For 'observed' and 'denied'-type events, this is also where they started.
If they moved "outside the system" (entering through a door with no entry zone, or exiting through a door with no exit zone), there will be no href. The href will also be missing if your operator does not have the right to view it.
The name was correct at the time of the event.

**type:
string

moved,
                                  observed,
                                  denied:** This enum divides location events into three types:
moved means a cardholder moved through a door from one zone to another. Whether they used a plastic card, a mobile credential, biometrics, a QR code, a vehicle plate, or something implemented by a third-party integration, provided they were granted access, this field's value will be moved.
denied means the cardholder requested access at a door but failed the access check.
observed means they did something that indicated their location, such as logging in to a workstation or an alarms terminal.

**cardholder:
object:** A block describing the cardholder whose location is recorded by the event.

**name:
string



The cardholder's name at the time of the event.


href:
string
(url)



The cardholder item's href. This will only be present if your operator has the right to view the cardholder.:** The cardholder's name at the time of the event.

**name:
string:** The cardholder item's href. This will only be present if your operator has the right to view the cardholder.

**href:
string
(url):** The href and name of the access zone or reception that the cardholder was in before this event occurred.
It is only useful (and present) for 'moved'-type events, which happen when a cardholder changes location. For 'observed' and 'denied'-type events, which happen when a cardholder does not move, afterLocation shows where they were.
The name was correct at the time of the event. The href will be missing if your operator does not have the right to view it.

**beforeLocation:
object:** The href and name of the access zone or reception that the cardholder was in after the event. For 'observed' and 'denied'-type events, this is also where they started.
If they moved "outside the system" (entering through a door with no entry zone, or exiting through a door with no exit zone), there will be no href. The href will also be missing if your operator does not have the right to view it.
The name was correct at the time of the event.

**name:
string:** The cardholder's name at the time of the event.

**href:
string
(url):** The cardholder item's href. This will only be present if your operator has the right to view the cardholder.

This is a mandatory field in an event. Without it, the POST will fail.

It is mandatory because the server cannot assume a reasonable default. It can for the others.

If your server is running 8.90 or later, eventType is a synonym. If you send both a type block and an eventType block it will use eventType.

Take this href from the list of
                              event types. Note that you can only use event types in one of the thirty external event groups with IDs 57-66 and 190-209. Command Centre ships with one event type per group, IDs 4000-4009 and 6010-6029, but you can create 970 more using the External Event Type Configuration utility.

Synonym for type, introduced in 8.90.

This block should contain the href of the item you wish to use as the source of your event. It can be any site item to which your operator has view access including all hardware, access zones, fence zones, doors, lockers, car parks, servers, external systems, and many other item types. If you do not supply one Command Centre will use the REST Client item identified by the API key in the Authorization header.

Cardholders cannot be event sources. To relate a cardholder to your event, use the cardholder block.

Make sure that your operator has the 'Create Events and Alarms' privilege on this item's division. 8.90 and later insist on it.

The API will use the source's division as the event's division.

Get the href using the API controller for that item type (such as
                              doors) or from /items.

It is not possible to submit an event with priority zero in the body, but if you submit an event with no priority it will use the one on the event type's action plan, which can be zero.

Like all other fields in this POST apart from the type, this field is optional. If you send it, it must be in the format described
                        here.

If you do not send this, the server will use the time that it received your request (its "now").

This is the first thing an operator will see when they look at this event. Some interactive clients do not give it a lot of room on screen so put the important parts of your message first. It has a limit of 1024 characters.

Command Centre will attach this string to event, as it does the message, but operators will have to look more closely at the event to see it. On the upside, it can be longer than the message: 2048 characters in 8.10.

If you wish to attach a cardholder to your event, link it here. Reports can show or filter by the cardholder.

This can be the href of any cardholder to which your operator has view access. Get the href from the
                              cardholders controller.

If you wish to attach an operator to your event, link it here. Like the cardholder, reports can show or filter by the operator.

This can be the href of any cardholder to which your operator has view access. It does not need to be an operator (a member of an operator group).

If you wish to attach an access zone to your event, link it here. Reports can filter by and show the entry access zone on events.

This can be the href of any access zone to which your operator has view access. Get the href using the
                              access zones controller or from /items.

If you wish to attach an access group to your event, link it here.

Unlike cardholders, operators, and entry access zones, access groups do not appear in Command Centre activity reports. You can add a filter to restrict an activity report by access groups, but the group that allowed an event into the report will not appear in a column.

Like all the other items you link to your event it will, of course, appear when you GET the event from the API later.

This can be the href of any access group to which your operator has view access. Get the href using the
                              groups or
                              items controller.

If you wish to attach a locker bank to your event, link it here.

Like an event's access group, you can filter a Command Centre activity report to events that involve a locker bank, but the bank will not appear in the report itself.

If you link both a locker and a locker bank to an event, Command Centre does not require that the locker is in the locker bank, but you may find that downstream reporting software misbehaves when it is not.

This can be the href of any locker bank to which your operator has view access. Get the href using the
                              locker banks or
                              items controller.

If you wish to link a locker to your event, do it here. Like an event's access group and locker bank, you can filter a Command Centre activity report to events that involve a locker, but the locker will not appear in the report itself.

This can be the href of any locker your operator can view. Get the href from the
                              items controller or the lockers field of
                              a locker bank.

If you wish to link a door to your event for later extraction or a report filter, do it here.

This can be the href of any door your operator can see. Get the href using the
                              doors or
                              items controller.

**type:
object:** This is a mandatory field in an event. Without it, the POST will fail.
It is mandatory because the server cannot assume a reasonable default. It can for the others.
If your server is running 8.90 or later, eventType is a synonym. If you send both a type block and an eventType block it will use eventType.

**href:
string
(url)



Take this href from the list of
                              event types. Note that you can only use event types in one of the thirty external event groups with IDs 57-66 and 190-209. Command Centre ships with one event type per group, IDs 4000-4009 and 6010-6029, but you can create 970 more using the External Event Type Configuration utility.:** Take this href from the list of
                              event types. Note that you can only use event types in one of the thirty external event groups with IDs 57-66 and 190-209. Command Centre ships with one event type per group, IDs 4000-4009 and 6010-6029, but you can create 970 more using the External Event Type Configuration utility.

**href:
string
(url):** Synonym for type, introduced in 8.90.

**eventType:
object:** This block should contain the href of the item you wish to use as the source of your event. It can be any site item to which your operator has view access including all hardware, access zones, fence zones, doors, lockers, car parks, servers, external systems, and many other item types. If you do not supply one Command Centre will use the REST Client item identified by the API key in the Authorization header.
Cardholders cannot be event sources. To relate a cardholder to your event, use the cardholder block.
Make sure that your operator has the 'Create Events and Alarms' privilege on this item's division. 8.90 and later insist on it.
The API will use the source's division as the event's division.

**href:
string
(url):** Get the href using the API controller for that item type (such as
                              doors) or from /items.

**href:
string
(url):** It is not possible to submit an event with priority zero in the body, but if you submit an event with no priority it will use the one on the event type's action plan, which can be zero.

**source:
object:** Like all other fields in this POST apart from the type, this field is optional. If you send it, it must be in the format described
                        here.
If you do not send this, the server will use the time that it received your request (its "now").

**href:
string
(url)



Get the href using the API controller for that item type (such as
                              doors) or from /items.:** This is the first thing an operator will see when they look at this event. Some interactive clients do not give it a lot of room on screen so put the important parts of your message first. It has a limit of 1024 characters.

**href:
string
(url):** Command Centre will attach this string to event, as it does the message, but operators will have to look more closely at the event to see it. On the upside, it can be longer than the message: 2048 characters in 8.10.

**priority:
integer
1 ≤ x ≤ 9:** If you wish to attach a cardholder to your event, link it here. Reports can show or filter by the cardholder.

**time:
string
(date-time):** This can be the href of any cardholder to which your operator has view access. Get the href from the
                              cardholders controller.

**message:
string:** If you wish to attach an operator to your event, link it here. Like the cardholder, reports can show or filter by the operator.

**details:
string:** This can be the href of any cardholder to which your operator has view access. It does not need to be an operator (a member of an operator group).

**cardholder:
object:** If you wish to attach an access zone to your event, link it here. Reports can filter by and show the entry access zone on events.

**href:
string
(url)



This can be the href of any cardholder to which your operator has view access. Get the href from the
                              cardholders controller.:** This can be the href of any access zone to which your operator has view access. Get the href using the
                              access zones controller or from /items.

**href:
string
(url):** If you wish to attach an access group to your event, link it here.
Unlike cardholders, operators, and entry access zones, access groups do not appear in Command Centre activity reports. You can add a filter to restrict an activity report by access groups, but the group that allowed an event into the report will not appear in a column.
Like all the other items you link to your event it will, of course, appear when you GET the event from the API later.

**operator:
object:** This can be the href of any access group to which your operator has view access. Get the href using the
                              groups or
                              items controller.

**href:
string
(url)



This can be the href of any cardholder to which your operator has view access. It does not need to be an operator (a member of an operator group).:** If you wish to attach a locker bank to your event, link it here.
Like an event's access group, you can filter a Command Centre activity report to events that involve a locker bank, but the bank will not appear in the report itself.
If you link both a locker and a locker bank to an event, Command Centre does not require that the locker is in the locker bank, but you may find that downstream reporting software misbehaves when it is not.

**href:
string
(url):** This can be the href of any locker bank to which your operator has view access. Get the href using the
                              locker banks or
                              items controller.

**entryAccessZone:
object:** If you wish to link a locker to your event, do it here. Like an event's access group and locker bank, you can filter a Command Centre activity report to events that involve a locker, but the locker will not appear in the report itself.

**href:
string
(url)



This can be the href of any access zone to which your operator has view access. Get the href using the
                              access zones controller or from /items.:** This can be the href of any locker your operator can view. Get the href from the
                              items controller or the lockers field of
                              a locker bank.

**href:
string
(url):** If you wish to link a door to your event for later extraction or a report filter, do it here.

**accessGroup:
object:** This can be the href of any door your operator can see. Get the href using the
                              doors or
                              items controller.

**href:
string
(url):** Take this href from the list of
                              event types. Note that you can only use event types in one of the thirty external event groups with IDs 57-66 and 190-209. Command Centre ships with one event type per group, IDs 4000-4009 and 6010-6029, but you can create 970 more using the External Event Type Configuration utility.

**href:
string
(url):** Get the href using the API controller for that item type (such as
                              doors) or from /items.

**href:
string
(url):** This can be the href of any cardholder to which your operator has view access. Get the href from the
                              cardholders controller.

**href:
string
(url):** This can be the href of any cardholder to which your operator has view access. It does not need to be an operator (a member of an operator group).

**href:
string
(url):** This can be the href of any access zone to which your operator has view access. Get the href using the
                              access zones controller or from /items.

**href:
string
(url):** This can be the href of any access group to which your operator has view access. Get the href using the
                              groups or
                              items controller.

**href:
string
(url):** This can be the href of any locker bank to which your operator has view access. Get the href using the
                              locker banks or
                              items controller.

**href:
string
(url):** This can be the href of any locker your operator can view. Get the href from the
                              items controller or the lockers field of
                              a locker bank.

**href:
string
(url):** This can be the href of any door your operator can see. Get the href using the
                              doors or
                              items controller.

Calls to /api/events/groups/ return this object, which is a named array of groups of event types.

An array of event group objects. There will be about 150. Most groups contain fewer than 100 event types; one contains around 200.

The alphanumeric ID of the event group. Use this ID in the group filter when requesting events.

The name of the event group.

An array of all the event types in the group.

The alphanumeric ID of the event type. Use this ID in the type filter when requesting events.

**eventGroups:
object[]:** An array of event group objects. There will be about 150. Most groups contain fewer than 100 event types; one contains around 200.

**object





id:
string



The alphanumeric ID of the event group. Use this ID in the group filter when requesting events.


name:
string



The name of the event group.


eventTypes:
object[]



An array of all the event types in the group.



object





id:
string



The alphanumeric ID of the event type. Use this ID in the type filter when requesting events.


name:
string:** The alphanumeric ID of the event group. Use this ID in the group filter when requesting events.

**id:
string:** The name of the event group.

**name:
string:** An array of all the event types in the group.

**eventTypes:
object[]:** The alphanumeric ID of the event type. Use this ID in the type filter when requesting events.

**id:
string:** The alphanumeric ID of the event group. Use this ID in the group filter when requesting events.

**name:
string:** The name of the event group.

**eventTypes:
object[]:** An array of all the event types in the group.

**object





id:
string



The alphanumeric ID of the event type. Use this ID in the type filter when requesting events.


name:
string:** The alphanumeric ID of the event type. Use this ID in the type filter when requesting events.

**id:
string:** The alphanumeric ID of the event type. Use this ID in the type filter when requesting events.

Calls inside /api/divisions/ return this object, which is simply a named array of objects each containing some information about a division.

An array of division objects.

A site generally does not have too many divisions. You should be able to collect them all in one request by using the top parameter. If not, follow this link to get the next page of divisions.

**results:


Division:** An array of division objects.

**Division:** A site generally does not have too many divisions. You should be able to collect them all in one request by using the top parameter. If not, follow this link to get the next page of divisions.

When a REST call returns the division of a Command Centre item such as a cardholder or access group, or when you ask it for the divisions in which an operator has a particular privilege, it will give an href inside /divisions/. Following that href will return one of these.

A self reference.

The alphanumeric ID of the division. Use this ID in the division filter when requesting events.

The division's name.

The division's description. New in 8.50. Not sent by default; ask for it with fields.

If you are running a multi-server installation and this item is homed on a remote server, this field will contain the name of that server. This field is missing from items that are held on the machine that served the API request. Added in 8.40.

An object containing an href to the division entity representing the current division's parent. GET the href for full details.

An object containing the division's visitor management configuration. It only appears if you ask for it with fields=visitorManagement and if your operator has the necessary privilege ('View Site', 'Edit Site', 'View Visits', 'Edit Visits', or 'Manage Receptions').

If present and true, this division has its own visitor management configuration. Otherwise it uses its parent's. That is also indicated by the presence or absence of the visitorTypes block.

This is an array of items, each containing a 'visitor type'. A visitor type comprises three things: an access group, to which visitor management will add every visitor as soon as they are on the visit, host access groups, to one of which the host cardholder must belong, and visitor access groups, to which Command Centre will add a visitor when they sign in.

When creating a visit you must pick a visitor type that is in the same division as the visit's reception. The visitor type you pick determines which host you can assign (he or she must be a member of at least one of the visitor type's host access groups) and which visitor access groups you can assign (they must be in the visitor type's list of visitor access groups).

This is the href you should use for a visitor type when you create or update a visit.

It is only used for identification: GETting it will 404.

The name and href of an access group.

Command Centre will add cardholders to this access group as soon as you add them to a visit of this type. The purpose of this group is to grant access to PDFs for personal data that will help them sign in on the day, such as passport and driver's licence numbers, and photos.

Names and hrefs of more access groups. When you create or modify a visit of this type, its host cardholder must belong to one of these.

A host is the cardholder Command Centre notifies when a visitor arrives, and is ultimately responsible for the visitor while on site.

An array of access groups.

Command Centre will add visitors to a visit's 'visitor access groups' when they sign in. Their purpose is to grant visitors access through the site's doors so that they can move around the site.

When creating a visit using this visitor type, you can only pick visitor access groups from this list.

The server puts each group's name and href inside a block called accessGroup, rather than in the root of the array element, to allow for expansion in a future version.

The name and href of an access group. Command Centre will add the visitors on the visit to this access group when they sign in.

**href:
string
(url):** A self reference.

**id:
string:** The alphanumeric ID of the division. Use this ID in the division filter when requesting events.

**name:
string:** The division's name.

**description:
string:** The division's description. New in 8.50. Not sent by default; ask for it with fields.

**serverDisplayName:
string:** If you are running a multi-server installation and this item is homed on a remote server, this field will contain the name of that server. This field is missing from items that are held on the machine that served the API request. Added in 8.40.

**parent:
object:** An object containing an href to the division entity representing the current division's parent. GET the href for full details.

**href:
string
(url):** An object containing the division's visitor management configuration. It only appears if you ask for it with fields=visitorManagement and if your operator has the necessary privilege ('View Site', 'Edit Site', 'View Visits', 'Edit Visits', or 'Manage Receptions').

**href:
string
(url):** If present and true, this division has its own visitor management configuration. Otherwise it uses its parent's. That is also indicated by the presence or absence of the visitorTypes block.

**visitorManagement:
object:** This is an array of items, each containing a 'visitor type'. A visitor type comprises three things: an access group, to which visitor management will add every visitor as soon as they are on the visit, host access groups, to one of which the host cardholder must belong, and visitor access groups, to which Command Centre will add a visitor when they sign in.
When creating a visit you must pick a visitor type that is in the same division as the visit's reception. The visitor type you pick determines which host you can assign (he or she must be a member of at least one of the visitor type's host access groups) and which visitor access groups you can assign (they must be in the visitor type's list of visitor access groups).

**active:
boolean



If present and true, this division has its own visitor management configuration. Otherwise it uses its parent's. That is also indicated by the presence or absence of the visitorTypes block.


visitortypes:
object[]



This is an array of items, each containing a 'visitor type'. A visitor type comprises three things: an access group, to which visitor management will add every visitor as soon as they are on the visit, host access groups, to one of which the host cardholder must belong, and visitor access groups, to which Command Centre will add a visitor when they sign in.
When creating a visit you must pick a visitor type that is in the same division as the visit's reception. The visitor type you pick determines which host you can assign (he or she must be a member of at least one of the visitor type's host access groups) and which visitor access groups you can assign (they must be in the visitor type's list of visitor access groups).



object





href:
string
(url)



This is the href you should use for a visitor type when you create or update a visit.
It is only used for identification: GETting it will 404.


accessGroup:
object



The name and href of an access group.
Command Centre will add cardholders to this access group as soon as you add them to a visit of this type. The purpose of this group is to grant access to PDFs for personal data that will help them sign in on the day, such as passport and driver's licence numbers, and photos.





name:
string



href:
string
(url)






hostAccessGroups:
object[]



Names and hrefs of more access groups. When you create or modify a visit of this type, its host cardholder must belong to one of these.
A host is the cardholder Command Centre notifies when a visitor arrives, and is ultimately responsible for the visitor while on site.



object





accessGroup:
object






name:
string



href:
string
(url)











visitorAccessGroups:
object[]



An array of access groups.
Command Centre will add visitors to a visit's 'visitor access groups' when they sign in. Their purpose is to grant visitors access through the site's doors so that they can move around the site.
When creating a visit using this visitor type, you can only pick visitor access groups from this list.
The server puts each group's name and href inside a block called accessGroup, rather than in the root of the array element, to allow for expansion in a future version.



object





accessGroup:
object



The name and href of an access group. Command Centre will add the visitors on the visit to this access group when they sign in.





name:
string



href:
string
(url):** This is the href you should use for a visitor type when you create or update a visit.
It is only used for identification: GETting it will 404.

**active:
boolean:** The name and href of an access group.
Command Centre will add cardholders to this access group as soon as you add them to a visit of this type. The purpose of this group is to grant access to PDFs for personal data that will help them sign in on the day, such as passport and driver's licence numbers, and photos.

**visitortypes:
object[]:** Names and hrefs of more access groups. When you create or modify a visit of this type, its host cardholder must belong to one of these.
A host is the cardholder Command Centre notifies when a visitor arrives, and is ultimately responsible for the visitor while on site.

**object





href:
string
(url)



This is the href you should use for a visitor type when you create or update a visit.
It is only used for identification: GETting it will 404.


accessGroup:
object



The name and href of an access group.
Command Centre will add cardholders to this access group as soon as you add them to a visit of this type. The purpose of this group is to grant access to PDFs for personal data that will help them sign in on the day, such as passport and driver's licence numbers, and photos.





name:
string



href:
string
(url)






hostAccessGroups:
object[]



Names and hrefs of more access groups. When you create or modify a visit of this type, its host cardholder must belong to one of these.
A host is the cardholder Command Centre notifies when a visitor arrives, and is ultimately responsible for the visitor while on site.



object





accessGroup:
object






name:
string



href:
string
(url)











visitorAccessGroups:
object[]



An array of access groups.
Command Centre will add visitors to a visit's 'visitor access groups' when they sign in. Their purpose is to grant visitors access through the site's doors so that they can move around the site.
When creating a visit using this visitor type, you can only pick visitor access groups from this list.
The server puts each group's name and href inside a block called accessGroup, rather than in the root of the array element, to allow for expansion in a future version.



object





accessGroup:
object



The name and href of an access group. Command Centre will add the visitors on the visit to this access group when they sign in.





name:
string



href:
string
(url):** An array of access groups.
Command Centre will add visitors to a visit's 'visitor access groups' when they sign in. Their purpose is to grant visitors access through the site's doors so that they can move around the site.
When creating a visit using this visitor type, you can only pick visitor access groups from this list.
The server puts each group's name and href inside a block called accessGroup, rather than in the root of the array element, to allow for expansion in a future version.

**href:
string
(url):** The name and href of an access group. Command Centre will add the visitors on the visit to this access group when they sign in.

**active:
boolean:** If present and true, this division has its own visitor management configuration. Otherwise it uses its parent's. That is also indicated by the presence or absence of the visitorTypes block.

**visitortypes:
object[]:** This is an array of items, each containing a 'visitor type'. A visitor type comprises three things: an access group, to which visitor management will add every visitor as soon as they are on the visit, host access groups, to one of which the host cardholder must belong, and visitor access groups, to which Command Centre will add a visitor when they sign in.
When creating a visit you must pick a visitor type that is in the same division as the visit's reception. The visitor type you pick determines which host you can assign (he or she must be a member of at least one of the visitor type's host access groups) and which visitor access groups you can assign (they must be in the visitor type's list of visitor access groups).

**object





href:
string
(url)



This is the href you should use for a visitor type when you create or update a visit.
It is only used for identification: GETting it will 404.


accessGroup:
object



The name and href of an access group.
Command Centre will add cardholders to this access group as soon as you add them to a visit of this type. The purpose of this group is to grant access to PDFs for personal data that will help them sign in on the day, such as passport and driver's licence numbers, and photos.





name:
string



href:
string
(url)






hostAccessGroups:
object[]



Names and hrefs of more access groups. When you create or modify a visit of this type, its host cardholder must belong to one of these.
A host is the cardholder Command Centre notifies when a visitor arrives, and is ultimately responsible for the visitor while on site.



object





accessGroup:
object






name:
string



href:
string
(url)











visitorAccessGroups:
object[]



An array of access groups.
Command Centre will add visitors to a visit's 'visitor access groups' when they sign in. Their purpose is to grant visitors access through the site's doors so that they can move around the site.
When creating a visit using this visitor type, you can only pick visitor access groups from this list.
The server puts each group's name and href inside a block called accessGroup, rather than in the root of the array element, to allow for expansion in a future version.



object





accessGroup:
object



The name and href of an access group. Command Centre will add the visitors on the visit to this access group when they sign in.





name:
string



href:
string
(url):** This is the href you should use for a visitor type when you create or update a visit.
It is only used for identification: GETting it will 404.

**href:
string
(url):** The name and href of an access group.
Command Centre will add cardholders to this access group as soon as you add them to a visit of this type. The purpose of this group is to grant access to PDFs for personal data that will help them sign in on the day, such as passport and driver's licence numbers, and photos.

**accessGroup:
object:** Names and hrefs of more access groups. When you create or modify a visit of this type, its host cardholder must belong to one of these.
A host is the cardholder Command Centre notifies when a visitor arrives, and is ultimately responsible for the visitor while on site.

**name:
string



href:
string
(url):** An array of access groups.
Command Centre will add visitors to a visit's 'visitor access groups' when they sign in. Their purpose is to grant visitors access through the site's doors so that they can move around the site.
When creating a visit using this visitor type, you can only pick visitor access groups from this list.
The server puts each group's name and href inside a block called accessGroup, rather than in the root of the array element, to allow for expansion in a future version.

**name:
string:** The name and href of an access group. Command Centre will add the visitors on the visit to this access group when they sign in.

**href:
string
(url):** This is the href you should use for a visitor type when you create or update a visit.
It is only used for identification: GETting it will 404.

**accessGroup:
object:** The name and href of an access group.
Command Centre will add cardholders to this access group as soon as you add them to a visit of this type. The purpose of this group is to grant access to PDFs for personal data that will help them sign in on the day, such as passport and driver's licence numbers, and photos.

**name:
string



href:
string
(url):** Names and hrefs of more access groups. When you create or modify a visit of this type, its host cardholder must belong to one of these.
A host is the cardholder Command Centre notifies when a visitor arrives, and is ultimately responsible for the visitor while on site.

**name:
string:** An array of access groups.
Command Centre will add visitors to a visit's 'visitor access groups' when they sign in. Their purpose is to grant visitors access through the site's doors so that they can move around the site.
When creating a visit using this visitor type, you can only pick visitor access groups from this list.
The server puts each group's name and href inside a block called accessGroup, rather than in the root of the array element, to allow for expansion in a future version.

**href:
string
(url):** The name and href of an access group. Command Centre will add the visitors on the visit to this access group when they sign in.

**accessGroup:
object:** The name and href of an access group. Command Centre will add the visitors on the visit to this access group when they sign in.

This is an example of a PATCH you could use to update a division, and a POST you could use to create one.

When POSTing, parent is mandatory.

The division's name. If you supply a name and another division already exists with that name, the call will fail. If you leave it blank in a POST, Command Centre will pick value for you.

The division's description.

A string, able to me much longer than description, suitable for holding notes about the division.

An object containing an href to the division entity representing the current division's parent.

Required when creating a new division, because only root divisions can be unparented and you cannot create a new one of those.

**name:
string:** The division's name. If you supply a name and another division already exists with that name, the call will fail. If you leave it blank in a POST, Command Centre will pick value for you.

**description:
string:** The division's description.

**notes:
string:** A string, able to me much longer than description, suitable for holding notes about the division.

**parent:
object:** An object containing an href to the division entity representing the current division's parent.
Required when creating a new division, because only root divisions can be unparented and you cannot create a new one of those.

Calls to /api/items return this object.

A list of item objects, each containing the alphanumeric ID of the item, its name, and its type.

An href to the next page of results. Missing if there are no more results.

**results:


Item summary:** A list of item objects, each containing the alphanumeric ID of the item, its name, and its type.

**Item summary:** An href to the next page of results. Missing if there are no more results.

/api/items returns an array of these. Because it can contain an item of any type, it only contains some fields that are common to all item types.

Use this ID in the source filter when requesting events to limit events to those with that source. Card events such as 'access granted', for example, have a door as their source.

The type object contains the ID and name of the item's type.

Use this ID in the type filter when requesting items if you wish to restrict the results to items of a certain type.

A human-readable name of the item's type, suitable for display.

This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.

If you are running a multi-server installation and this item is homed on a remote server, this field will contain the name of that server. This field is missing from items that are held on the machine that served the API request. Added in 8.40.

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

**id:
string:** Use this ID in the source filter when requesting events to limit events to those with that source. Card events such as 'access granted', for example, have a door as their source.

**name:
string:** The type object contains the ID and name of the item's type.

**type:
object:** Use this ID in the type filter when requesting items if you wish to restrict the results to items of a certain type.

**id:
string



Use this ID in the type filter when requesting items if you wish to restrict the results to items of a certain type.


name:
string



A human-readable name of the item's type, suitable for display.
This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.:** A human-readable name of the item's type, suitable for display.
This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.

**id:
string:** If you are running a multi-server installation and this item is homed on a remote server, this field will contain the name of that server. This field is missing from items that are held on the machine that served the API request. Added in 8.40.

**name:
string:** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**id:
string:** Use this ID in the type filter when requesting items if you wish to restrict the results to items of a certain type.

**name:
string:** A human-readable name of the item's type, suitable for display.
This string is translated using the installation's language pack, and Gallagher reserves the right to change item type names in new versions, so you should not do anything with this string except show it to a person.

A call to /api/items/{id} (added in 8.40) returns one of these. It adds the item's division to the summary object.

Calls to /api/items/types return this object, an array of item types.

A list of item types.

The alphanumeric ID of the item type. Use this ID in the type filter when requesting items.

The name of the item type.

**itemTypes:
object[]:** A list of item types.

**object





id:
string



The alphanumeric ID of the item type. Use this ID in the type filter when requesting items.


name:
string



The name of the item type.:** The alphanumeric ID of the item type. Use this ID in the type filter when requesting items.

**id:
string:** The name of the item type.

**id:
string:** The alphanumeric ID of the item type. Use this ID in the type filter when requesting items.

**name:
string:** The name of the item type.

POSTs and GETs to /api/items/updates return this object. It contains an array of updates and a next link to collect more.

Added in 8.30.

A list of items and their statuses.

On your first two calls -- the POST and the first GET -- this will contain all the items in your subscription (provided they are the types of item that have statuses). On subsequent GETs it will only contain the items that received status updates since your previous call. Note that an item will be in this array if it received any status update at all, even if the status flags did not change. Prepare to receive updates that do not contain novel data.

This array is not paginated: it could contain every one of the items you put in your POST.

After about 50 seconds the call will time out and return an empty array here.

The item's ID, from the list you sent in the POST that created this subscription.

An array of string enumerations (flags) that describe the item's condition in a reliable, machine-readable way.

The item types that the REST API supports (such as fence zones and inputs) have a full set of status flags, described in
                                  their own sections of this documentation.

Items that the REST API does not support yet (such as readers) will return error flags if they are in an unusual state, or nothing if they are online and reporting normally. Therefore an empty array is a good sign.

The state of the item in a multi-line string taken from the server's language pack.

The item's status text with end-of-line characters replaced by spaces.

A link to GET more status updates. Do not wait longer than thirty seconds before using this link or your subscription will expire and you will need to submit another POST to create a new one.

**updates:
object[]:** A list of items and their statuses.
On your first two calls -- the POST and the first GET -- this will contain all the items in your subscription (provided they are the types of item that have statuses). On subsequent GETs it will only contain the items that received status updates since your previous call. Note that an item will be in this array if it received any status update at all, even if the status flags did not change. Prepare to receive updates that do not contain novel data.
This array is not paginated: it could contain every one of the items you put in your POST.
After about 50 seconds the call will time out and return an empty array here.

**object





id:
string



The item's ID, from the list you sent in the POST that created this subscription.


statusFlags:
array



An array of string enumerations (flags) that describe the item's condition in a reliable, machine-readable way.
The item types that the REST API supports (such as fence zones and inputs) have a full set of status flags, described in
                                  their own sections of this documentation.
Items that the REST API does not support yet (such as readers) will return error flags if they are in an unusual state, or nothing if they are online and reporting normally. Therefore an empty array is a good sign.


statusText:
string



The state of the item in a multi-line string taken from the server's language pack.


status:
string



The item's status text with end-of-line characters replaced by spaces.:** The item's ID, from the list you sent in the POST that created this subscription.

**id:
string:** An array of string enumerations (flags) that describe the item's condition in a reliable, machine-readable way.
The item types that the REST API supports (such as fence zones and inputs) have a full set of status flags, described in
                                  their own sections of this documentation.
Items that the REST API does not support yet (such as readers) will return error flags if they are in an unusual state, or nothing if they are online and reporting normally. Therefore an empty array is a good sign.

**statusFlags:
array:** The state of the item in a multi-line string taken from the server's language pack.

**statusText:
string:** The item's status text with end-of-line characters replaced by spaces.

**status:
string:** A link to GET more status updates. Do not wait longer than thirty seconds before using this link or your subscription will expire and you will need to submit another POST to create a new one.

**id:
string:** The item's ID, from the list you sent in the POST that created this subscription.

**statusFlags:
array:** An array of string enumerations (flags) that describe the item's condition in a reliable, machine-readable way.
The item types that the REST API supports (such as fence zones and inputs) have a full set of status flags, described in
                                  their own sections of this documentation.
Items that the REST API does not support yet (such as readers) will return error flags if they are in an unusual state, or nothing if they are online and reporting normally. Therefore an empty array is a good sign.

**statusText:
string:** The state of the item in a multi-line string taken from the server's language pack.

**status:
string:** The item's status text with end-of-line characters replaced by spaces.

Contains a list of item IDs (short alphanums). Send it in the body of a
                    POST to create a subscription to status updates to the items with these IDs.
