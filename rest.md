# Command Centre REST API: Non-cardholder items
            API Reference

### Licensing

### Versions

### Efficiency tips

#### API changes on the roadmap

#### Changes in 8.80

#### Changes in 8.70

#### Changes in 8.60

#### Changes in 8.50

#### Changes in 8.30

#### Changes in 8.20

#### Changes in 8.10

This document describes how you can use the Command Centre REST API to view and manage access zones, alarm zones, elevator groups, fence zones, doors, macros, inputs, and outputs.

It has companion documents describing the
                  Cardholders and
                  Alarms and Events APIs, and Command Centre's handling of
                  PIV cards.

It assumes some familiarity with HTTP and REST interfaces. Before you start developing against this interface, you should also read the
                  Authentication section to learn how to format your queries, and Command Centre's Configuration Client online help to learn how to ready the server to receive them (search for 'REST API').

Each section contains a list of use cases intended as quick solutions for simple tasks or a how-to to get you started toward your particular goal. However they also serve as a good introduction to the API if you step through them using a web browser, starting at /api on port 8904 of your Command Centre server. To do that, you will need to set up Command Centre, and install a browser plugin on your web client that lets you set an Authorization HTTP header, and a JSON formatter to prettify the results documents. Search the Configuration client's online help for 'test REST API' for guidance.

In versions up to and including 8.50, all of the GET calls described here require the RESTStatus licence. All the POSTs require RESTOverrides. The two licences do not overlap: to watch an item and override it, you will need both.

In 8.60 and later the GETs return a limited field set if you have RESTOverrides but not RESTStatus - enough to let you find the item you want to override.

The server will return a 403 if you attempt an operation for which the server is not licensed.

The body of this document clearly indicates when recent features arrived in the API so that readers with older versions of Command Centre know not to expect them.

The server returns 1000 items by default, instead of 100.

A new query parameter requested_by lets you attribute overrides to another cardholder. Your operator must have the 'Delegate API Activity' privilege in the cardholder's division.

Item updates now include fields that changed to an empty value. Versions up to 8.60 returned an update but did not tell you which field changed or what it changed to, if it changed to a blank.

The server property that turns off
                      client certificate checking changed.

A
                      schedules API providing CRUD of the six schedule item types.

A
                      day categories API that returns the day categories you need for those schedules.

A
                      pulse override on outputs.

A 'connectedController' field on the item types that did not already have it.

An
                      elevator groups API providing a view of elevator groups so that you can
                      set a cardholder's default floor.

All changes to this document contain the text "8.30".

All changes to this document contain the text "8.20".

All changes to this document contain the text "8.10".

When downloading more than one page of items, sort by ID.

Set your page size as large as your client can handle. The default was 100 before 8.70; using the top parameter to take it to 1000 or more gives
                      much better throughput.

(In v8.00 or later) avoid the details page by adding detail fields to the summary page with the
                      fields parameter. One summary page of 1,000 items, while huge, is much quicker to retrieve than 1,000 detail pages.

For example, to get the names, doors, and override commands of all your access zones:

GET /api/access_zones?fields=name,doors,commands

(In v8.00 or later) save work generating the details page by using the
                      fields parameter to reduce the amount of data on each page. Be explicit: do not use fields=defaults, unless you need every field that 'defaults' gives you.

When searching for an item by name, if you have its exact name, put it in quotes "...". Otherwise Command Centre will perform a substring search, which is slower and may return more results than you need. Even with quotes, the search is case-insensitive.

- A future version will sort its search results differently if you do not use a query parameter to override the default.

- Early-adopter support for
                    Interlock groups.

- The server returns 1000 items by default, instead of 100.

- A new query parameter requested_by lets you attribute overrides to another cardholder. Your operator must have the 'Delegate API Activity' privilege in the cardholder's division.

- Item updates now include fields that changed to an empty value. Versions up to 8.60 returned an update but did not tell you which field changed or what it changed to, if it changed to a blank.

- The RESTOverrides licence allows you to find items and their override URLs.

- You can search for items by description.

- The server property that turns off
                      client certificate checking changed.

- A
                      schedules API providing CRUD of the six schedule item types.

- A
                      day categories API that returns the day categories you need for those schedules.

- A
                      pulse override on outputs.

- A 'connectedController' field on the item types that did not already have it.

- An
                      elevator groups API providing a view of elevator groups so that you can
                      set a cardholder's default floor.

- A 'connectedController' field in the
                    door detail that returns the ID and name of the door's hardware controller (a C6000 controller, not an API controller).

- An
                    update_cardholder_location call on the Access Zones API that returns you the list of Access Zones into which your operator is allowed to move cardholders.

- An
                    inputs API that gives you the state of 'input' hardware items and lets you override them. It is very similar to the outputs API.

- When downloading more than one page of items, sort by ID.

- Set your page size as large as your client can handle. The default was 100 before 8.70; using the top parameter to take it to 1000 or more gives
                      much better throughput.

- (In v8.00 or later) avoid the details page by adding detail fields to the summary page with the
                      fields parameter. One summary page of 1,000 items, while huge, is much quicker to retrieve than 1,000 detail pages.
For example, to get the names, doors, and override commands of all your access zones:
 GET /api/access_zones?fields=name,doors,commands

- (In v8.00 or later) save work generating the details page by using the
                      fields parameter to reduce the amount of data on each page. Be explicit: do not use fields=defaults, unless you need every field that 'defaults' gives you.

- When searching for an item by name, if you have its exact name, put it in quotes "...". Otherwise Command Centre will perform a substring search, which is slower and may return more results than you need. Even with quotes, the search is case-insensitive.

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

### Subscribing to status updates

### Staying current

### For a user interface

### For an integration

#### Abnormal status flags

Most items have a status. Access zones, for example, can be in one of a few different modes, and have a
                  zone count that rises and falls with cardholders arriving and leaving. Inputs and outputs can be on or off. Any item can be in an unknown state when the REST server is out of touch with its hardware. Et cetera.

The REST API can send an item's status to you in a string ready for human consumption in a UI, or as a list of flags more suitable for an integration. Neither of those fields are on an item's summary or details pages by default; to obtain them reliably you need to subscribe to updates.

The server does not always have an item's status: to prevent unnecessary work on the controller, network, and server, it will stop requesting updates when nothing is subscribed to them. It is very important that items stay in touch with their controllers, and controllers with each other, but it is normal for controllers to limit what they send back to the server.

The server will be up to date when one of the following happened recently:

If it is not up to date and you request the status on an item's summary or details page, the server will report that the status is unknown.

To retrieve the status of one item you should GET the item's updates link from its details page. To monitor the status of many items you should use the
                  status subscription API on the items controller (added in 8.30).

Whichever API you use, if the server does not have an item's status when you ask it will request an update from the item itself. For hardware items that may involve a conversation with another server, a hardware controller, and another piece of hardware on the end of a serial line. That all takes time, but the server answers you immediately, so the first response you receive may be 'unknown'. The status request continues in the background.

No matter what you receive, you should follow the next link in a loop until it gives you a status you are looking for, or another status indicating why it cannot. Your original request started a subscription, which every subsequent request refreshes, so while you stay in that loop the server will have the item's current status for you and all other callers.

The first status you will receive after 'unknown' will probably be 'controller unknown', which means that the controller (or other hardware) has received the server's request but does not yet have an answer, because of that serial line. You need something better, so stay in the loop.

When you use the updates link on the item's details page it will return immediately. When follow the next link in the page it returns, it will send back the status straight away if it changed since your previous call, or block until it does change. If nothing happens within a minute or so, it will return with no updates.

Whether you received an update or not, the body will contain a next link for you to follow for the next update.

State changes are not queued: the API only keeps the last, so it only takes one call to get yourself up to date with the server. Remember that the server itself may not have been up to date, and your making the call will have started it on its own little journey of discovery, which will cause more updates.

The 'Site' tab of the sample application shows this in operation.

The statusText field describes the state of the item in a multi-line string taken from the server's language pack. The natural state of a door, for example, is

A fence zone's status text could be

For one-line display, the status field is the same thing with spaces instead of line endings.

Neither of these is intended for integrations. By all means display them in a user interface (as Gallagher software does) but do not attempt to parse information out of them.

The statusFlags field contains an array of string enumerations (flags) that describe the item's condition in a reliable and machine-readable way.

Each item type has a different set of flags. Some overlap (doors, inputs, and outputs can all be closed, for example) but most flags apply to only one kind of item. The common exceptions are the flags which indicate why the server cannot return the item's actual status, covered next.

These are the status flags that indicate the server does not have the current status of the item:

unsaved and deleted are transient conditions you should not encounter in normal use. Either way, the item is in no condition to query.

unconfigured is very common while a site is being set up. It means the item is not fully configured yet. An access zone is not configured until it has at least one door, for example.

remoteServerOffline is only possible in a multi-server setup. It means that the item is remote (on a different server) and the server answering your REST query cannot reach it.

processOffline means the Controller service that is meant to be running on the Command Centre server, and handling all the communications with hardware controllers, is not.

controllerOffline means the software on the server is as it should be but the hardware controller (such as a C6000) is offline. That could mean it needs its certificate revalidated, or just that its power or networking is out.

notPolled means the item is shunted: Command Centre is ignoring the item's communications at the request of an operator. You normally do it to stop spurious alarms.

deviceNotResponding means exactly that. It means the server is in contact with the hardware controller, but there is a problem between there and the item. Probably a cable fault, unless encryptionKeysTampered accompanies it.

Those are fault conditions. If you see one of those, there is a configuration or hardware fault or a shunt preventing the server communicating with the item.

They are in priority order: if you receive one near the bottom of the list you can take heart in the knowledge that your item is suffering none of the preceding abnormalities.

There are two more flags that are common to most item types.

unknown means everything else is in order but you caught the server in a period when it simply had no need to stay in touch with the item. You will get this on a summary or details page, because they do not subscribe to updates for the item. The 'updates' link will not send you this status flag.

controllerUnknown is a transient state, hopefully. The 'updates' call often returns it the first time if the server does not have the item's status already. It means a component (such as a hardware controller) between the server and your item is working on bringing in an update. This generally resolves quickly, so if you follow the 'next' link you will receive the latest status.

The server will not send any other flags with one of those eleven, apart from the possible pairing of encryptionKeysTampered and deviceNotResponding, and some unusual combinations noted later.

Do not go looking for all of the abnormal status flags above and assume the best if they are not there. Gallagher may add more fault conditions in future versions of the API.

Instead, read each item's section under Operations for what its flags will be when it is online. A paragraph called "flag rules" shows how you can tell if the item is in a correct state. For the impatient: doors, inputs, and outputs will be open or closed, fence zones will be on or off, and alarm and access zones will be in one of four zone states.

- an operator had the item visible in the Configuration, Command Centre, or mobile clients,

- an application monitored the item via another API,

- a REST client used the item in a
                    status subscription, or

- a REST client followed the item's updates link from its details page.

- unsaved and deleted are transient conditions you should not encounter in normal use. Either way, the item is in no condition to query.

- unconfigured is very common while a site is being set up. It means the item is not fully configured yet. An access zone is not configured until it has at least one door, for example.

- remoteServerOffline is only possible in a multi-server setup. It means that the item is remote (on a different server) and the server answering your REST query cannot reach it.

- processOffline means the Controller service that is meant to be running on the Command Centre server, and handling all the communications with hardware controllers, is not.

- controllerOffline means the software on the server is as it should be but the hardware controller (such as a C6000) is offline. That could mean it needs its certificate revalidated, or just that its power or networking is out.

- notPolled means the item is shunted: Command Centre is ignoring the item's communications at the request of an operator. You normally do it to stop spurious alarms.

- deviceNotResponding means exactly that. It means the server is in contact with the hardware controller, but there is a problem between there and the item. Probably a cable fault, unless encryptionKeysTampered accompanies it.

- unknown means everything else is in order but you caught the server in a period when it simply had no need to stay in touch with the item. You will get this on a summary or details page, because they do not subscribe to updates for the item. The 'updates' link will not send you this status flag.

- controllerUnknown is a transient state, hopefully. The 'updates' call often returns it the first time if the server does not have the item's status already. It means a component (such as a hardware controller) between the server and your item is working on bringing in an update. This generally resolves quickly, so if you follow the 'next' link you will receive the latest status.

### Overrides

### Status flags

### Use cases

#### Access Zone flag rules

#### Searching for access zones by name

#### Changing an access zone's access mode

#### Finding an access zone's status

These methods give you read access to Access Zones in the Command Centre database, and let you change their modes, lock them down, and send other overrides.

The first use case below introduces the main entry point. It is a paginated search interface that gives any number of access zones, each containing the fields you ask for in the query.

Note:

If the access zone is online, its statusFlags field may contain one or more of these flags:

saltoOutputOnly means the the access zone would be considered unconfigured because it is missing doors, and therefore offline, except that it has a Salto output number assigned to it. That is a normal operational configuration.

mobileZone also means the access zone would be considered unconfigured (missing doors) and therefore offline, except that a mobile reader is able to badge cardholders into it, making it a perfectly useful access zone. It will also have a secure flag.

lockedDown means only cardholders with the privilege to enter locked-down zones shall pass. The zone will also be 'secure'. When the lockdown override ends it will return to its previous access mode.

usePin means when you use a card at a reader to unlock the door or at a terminal to control an alarm zone, you will also need your PIN.

zoneCountTooHigh means the zone count is above its maximum.

zoneCountTooLow means the zone count is below its minimum.

zoneCountInGrace means the zone count recently became too low or high.

The following four give the zone's access state. An online access zone will always return one of them.

If and only if the zone online and not locked down, there will be exactly one of 'secure', 'dualAuth', 'codeOrCard', or 'free'.

If and only if the zone online and locked down, there will be exactly one of 'secure' or 'dualAuth'.

Because 'saltoOutputOnly' and 'mobileZone' are variations of the unconfigured offline condition, 'saltoOutputOnly' will be alone and only 'secure' will accompany 'mobileZone'.

If a zone has both a Salto output number and mobile access, only 'saltoOutputOnly' will appear.

If there is 'zoneCountInGrace' there will always be exactly one of 'zoneCountTooHigh' or 'zoneCountTooLow' (never both).

Using the first three rules above, your test for an access zone being in error is 'secure', 'dualAuth', 'codeOrCard', 'free', 'saltoOutputOnly', and 'mobileZone' all missing.

- End-times on overrides are not accurate to the second. Internally, Command Centre converts the end time to a duration, so you may find that submitting end times in the very near future does not have the exact effect you expect.

- An override without an end time lasts until the next mode change or 'cancel untimed overrides' entry in the access zone's schedule.

- The end time you set for an override cannot be in the past or more than 24 hours into the future.

- saltoOutputOnly means the the access zone would be considered unconfigured because it is missing doors, and therefore offline, except that it has a Salto output number assigned to it. That is a normal operational configuration.

- mobileZone also means the access zone would be considered unconfigured (missing doors) and therefore offline, except that a mobile reader is able to badge cardholders into it, making it a perfectly useful access zone. It will also have a secure flag.

- lockedDown means only cardholders with the privilege to enter locked-down zones shall pass. The zone will also be 'secure'. When the lockdown override ends it will return to its previous access mode.

- usePin means when you use a card at a reader to unlock the door or at a terminal to control an alarm zone, you will also need your PIN.

- zoneCountTooHigh means the zone count is above its maximum.

- zoneCountTooLow means the zone count is below its minimum.

- zoneCountInGrace means the zone count recently became too low or high.

- secure means the doors are locked.

- dualAuth means the doors are locked and - depending on configuration - cardholders will need a second credential to open them.

- codeOrCard means you can unlock a door either with a card, the zone's access code, or (if suitably configured on the reader) a personal user code. You will not see this flag if the zone is locked down.

- free means the zone's doors are unlocked. You will not see this when the zone is locked down.

- If and only if the zone online and not locked down, there will be exactly one of 'secure', 'dualAuth', 'codeOrCard', or 'free'.

- If and only if the zone online and locked down, there will be exactly one of 'secure' or 'dualAuth'.

- Because 'saltoOutputOnly' and 'mobileZone' are variations of the unconfigured offline condition, 'saltoOutputOnly' will be alone and only 'secure' will accompany 'mobileZone'.

- If a zone has both a Salto output number and mobile access, only 'saltoOutputOnly' will appear.

- If there is 'zoneCountInGrace' there will always be exactly one of 'zoneCountTooHigh' or 'zoneCountTooLow' (never both).

- GET /api.

- Follow the link at features.accessZones.accessZones.href
↪, appending a search term such as name=substring to filter the access zones, and fields to tell the server what to return about each. The next section covers those query parameters.

- Process the results, following the next link until there isn't one.

- Find the href for the access zone using the process above.

- GET it.

- Find the API URL you require in the commands structure of the results, such as free or securePin, from
                  the detail. Use the until variants if you are specifying an end time.

- POST to that URL. Some require a JSON object (resetting the zone count, and all those with until in their command block keys). The others expect an empty body.

- Find the href for the access zone using the process above, and GET it.

- Take the updates
↪ href from that page. For a version 8.00 server append the query parameter separator (? or &) then fields=defaults,zoneCount if you need the zone count as well as the default status fields. The zone count is included by default in 8.10 and later.

- GET it.

- Use the flag rules above to interpret the status flags you receive.

- Follow the next link to stay up to date.

This returns a summary of the access zones matching your search criteria.

The result will contain no more than 100 or 1000 access zones (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have the privilege to view any access zones, such as 'View Site', 'Edit Site', or 'Override'. Perhaps there are no access zones in the divisions in which your operator has privileges, or your operator has no privileges at all.

When you have loaded them all there will be no next link.

Take this URL from the 'href' field in the features.accessZones.accessZones section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

This returns a list of the access zones to which your operator is allowed to move cardholders, and a special zone you can use as a target to remove a cardholder from all access zones.

Like all other paginated queries in this API, the result will contain no more than 100 or 1000 access zones (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

When you have loaded them all there will be no next link.

If your result set is empty it means there are no access zones in the divisions in which your operator has the privilege to move cardholders ('Manage Cardholder Location'), or your operator does not have the privilege at all.

Take this URL from the 'href' field in the features.cardholders.updateLocationAccessZones section of /api.

Added in 8.20.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

The site does not have the RESTCardholders licence.

This returns the detail of one access zone.

Follow the 'href' field in an
                    access zone summary to get here.

(no description)

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

Success.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent an access zone, or the operator does not have a privilege on the zone's division that allows viewing access zones, such as 'View Site', 'Edit Site', or 'Override'."

Sends an override to an access zone to change its mode to 'free - no PIN', meaning the doors will be free and you will not need a PIN to perform an override on a terminal.

The same URL with _pin on the end will send an override to an access zone to change its mode to 'free - PIN', meaning you
                    will need a PIN on a terminal.

If you do not give it an end time in the body, the override will remain in place until the next scheduled change.

Optional

The ID of the access zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding access zones (such as 'Override').

Sends an override to an access zone to change its mode to 'secure - no PIN', meaning you will need a card, but not a PIN, to open its doors or perform overrides on terminals.

The same URL with _pin on the end will send an override to the access zone to change its mode to 'secure - PIN', meaning you
                    will need a PIN on the door and on a terminal.

If you do not give it an end time in the body, the override will remain in place until the next scheduled change.

Optional

The ID of the access zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding access zones (such as 'Override').

Sends an override to an access zone to change its mode to 'Code or Card - No PIN', meaning you can use your user code or the zone's 'code-only code' to open its doors, depending on the reader's configuration. You will not need a PIN on a terminal.

The same URL with _pin on the end will override the access zone into 'code or card - PIN', meaning you
                    will need a PIN when you use a card.

If you do not give it an end time in the body, the override will remain in place until the next scheduled change.

Optional

The ID of the access zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding access zones (such as 'Override').

Sends an override to an access zone to change its mode to 'dual auth - no PIN', meaning you will need two cardholders to open its doors, and they will not need a PIN. Terminal functions will require a card but no PIN.

The same URL with _pin on the end will override the zone into 'dual auth - PIN' mode, meaning each cardholder will need a PIN for access, and terminal functions will also require a PIN.

If you do not give it an end time in the body, the override will remain in place until the next scheduled change.

Optional

The ID of the access zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding access zones (such as 'Override').

Sends an override to an access zone to forgive anti-passback for all cardholders in the zone.

The ID of the access zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding access zones (such as 'Override').

Locks down an access zone. In this mode, cardholders will need the 'Entry allowed during lockdown' privilege to enter the zone, in addition to normal access.

It takes no parameters. The lockdown will remain in place until cancelled, or the access zone receives an override to another mode.

The ID of the access zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding access zones (such as 'Override').

Cancels a lockdown, returning it to its scheduled state. It will not cancel any other kind of override.

The ID of the access zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding access zones (such as 'Override').

Sets the count of cardholders inside a zone.

The new cardholder count for the zone.

The ID of the access zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding access zones (such as 'Override').

Cancels an override, returning the access zone to its scheduled state.

This command will achieve nothing if the alarm zone is not controlled by a schedule, because without a schedule the alarm zone does not have the concept of a 'normal' state.

It will not cancel a lockdown. For that you need cancel_lock_down.

The ID of the access zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding access zones (such as 'Override').

See the
                    item status topic for how to use the updates APIs.

Note that this API call is good for watching one item only; if you want to monitor several, you are better off with a
                    status subscription.

Follow the 'updates' field in an access zone
                    summary or
                    details pages to get here.

The ID of the access zone.

Returns these fields in the update, instead of the default set. Note that removing fields also saves you from updates to those fields.

Success. The
                        introduction describes the three status fields and the
                        access zone detail describes zoneCount.

8.50 and earlier: the site does not have the RESTStatus licence.

8.60 and later: the site has neither the RESTStatus nor RESTOverrides licence.

The request's URL does not represent an access zone, or the operator does not have a privilege on the zone's division that allows viewing access zones, such as 'View Site', 'Edit Site', or 'Override'."

### Overrides

### Status flags

### Use cases

#### Alarm Zone flag rules

#### Searching for alarm zones by name

#### Changing an alarm zone's state

#### Finding an alarm zone's status

These methods give you read access to Alarm Zones in the Command Centre database, and let you change their states: armed, disarmed, two user-defined states, and (if at least one Fence Zone is directing its events to this Alarm Zone) 'Armed - High Voltage' or 'Armed - Low Feel'.

Alarm zones may run by a schedule, but it is optional. Alarm zones that do not have a schedule change state only through manual overrides, meaning they do not have a natural state. That in turn means that you cannot override them for a duration -- they have no state to return to.

The first use case below introduces the main entry point. It is a paginated search interface that gives you any number of alarm zones, each containing the fields you ask for in the query.

Note:

If the alarm zone is online, its statusFlags field will contain one or more of these flags:

armed means the alarm zone is armed. It will be called that even if you have changed the terminology in the Server Properties.

disarmed as above.

user1 will be called that even if you have given it a different name in Server Properties.

user2 as above.

exitDelay means the zone is temporarily ignoring input triggers. One of the previous four flags shows you the state the zone is about to change away from.

entryDelay means an input has triggered the alarm zone but Command Centre is giving a cardholder the opportunity to disarm the zone.

lowFeel means the alarm zone has a fence zone attached, and that it is in 'low feel' mode (meaning it is delivering lower voltage than you would use for a strong deterrant).

highVoltage means that the alarm zone has a fence zone attached, and that it is 'high voltage' mode (meaning it is delivering a strong deterrant pulse).

- End-times on overrides are not accurate to the second. Internally, Command Centre converts the end time to a duration, so you may find that submitting end times in the very near future does not have the exact effect you expect.

- Submitting an override without an end time makes it take effect until the next state change or 'cancel untimed overrides' entry in the alarm zone's schedule.

- Overrides you submit via REST are not subject to the "manual unset" timeouts you can set in the Configuration Client. Those only affect readers, terminals, and pushbuttons.

- The end time you set for an override cannot be in the past or more than 24 hours into the future.

- armed means the alarm zone is armed. It will be called that even if you have changed the terminology in the Server Properties.

- disarmed as above.

- user1 will be called that even if you have given it a different name in Server Properties.

- user2 as above.

- exitDelay means the zone is temporarily ignoring input triggers. One of the previous four flags shows you the state the zone is about to change away from.

- entryDelay means an input has triggered the alarm zone but Command Centre is giving a cardholder the opportunity to disarm the zone.

- lowFeel means the alarm zone has a fence zone attached, and that it is in 'low feel' mode (meaning it is delivering lower voltage than you would use for a strong deterrant).

- highVoltage means that the alarm zone has a fence zone attached, and that it is 'high voltage' mode (meaning it is delivering a strong deterrant pulse).

- If and only if the zone is online, there will exactly one of 'armed', 'disarmed', 'user1', or 'user2'. That is your test for whether an alarm zone is in error.

- 'exitDelay' and 'entryDelay' cannot appear together.

- If and only if the zone is online and has a fence zone, there will be exactly one of 'lowFeel' or 'highVoltage'.

- GET /api.

- Follow the link at features.alarmZones.alarmZones.href
↪, appending a search term such as name=substring to filter the access zones, and fields to tell the server what to return about each. The next section covers those query parameters.

- Process the results, following the next link until there isn't one.

- Find the href for the alarm zone using the process above.

- GET it.

- Find the API URL you require in the commands structure of the results, such as arm
↪ or disarm
↪, using
                  the detail. Use the calls with Until on the ends of their names if you are specifying an end time.

- POST to that URL. All those with Until in their names require a JSON object in the body giving the time at which the override should end; the others do not.

- Find the href for the alarm zone using the process above, and GET it.

- Follow the updates
↪ href from that page.

- Use the flag rules above to interpret the status flags you receive.

- Follow the next link to stay up to date.

This returns a summary of the alarm zones matching your search criteria.

The result will contain no more than 100 or 1000 alarm zones (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have the privilege to view any alarm zones, such as 'View Site', 'Edit Site', or 'Override'. Perhaps there are no alarm zones in the divisions in which your operator has privileges, or your operator has no privileges at all.

When you have loaded them all there will be no next link.

Take this URL from the 'href' field in the features.alarmZones.alarmZones section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

This returns the detail of one alarm zone.

Follow the 'href' field in an
                    alarm zone summary to get here.

The ID of the alarm zone.

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

Success.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent an alarm zone, or the operator does not have a privilege on the zone's division that allows viewing alarm zones, such as 'View Site', 'Edit Site', or 'Override'."

Sends an override to an alarm zone to arm it.

If you send an end time in the body, and the alarm zone has a schedule to consult to find the state that it should return to, the override will only stay in effect until then.

If you do not, the override will remain in place until the next scheduled or manual change.

Optional

The ID of the alarm zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding alarm zones (such as 'Override').

Sends an override to an alarm zone to disarm it.

If you send an end time in the body, and the alarm zone has a schedule to consult to find the state that it should return to, the override will only stay in effect until then.

If you do not, the override will remain in place until the next scheduled or manual change.

Optional

The ID of the alarm zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding alarm zones (such as 'Override').

Sends an override to an alarm zone to set its state to 'user1', one of the custom states.

If you send an end time in the body, and the alarm zone has a schedule to consult to find the state that it should return to, the override will only stay in effect until then.

If you do not, the override will remain in place until the next scheduled or manual change.

Optional

The ID of the alarm zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding alarm zones (such as 'Override').

Sends an override to an alarm zone to set its state to 'user2', the other of of the custom states.

If you send an end time in the body, and the alarm zone has a schedule to consult to find the state that it should return to, the override will only stay in effect until then.

If you do not, the override will remain in place until the next scheduled or manual change.

Optional

The ID of the alarm zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding alarm zones (such as 'Override').

Sends an override to an alarm zone to set its state to 'armed - high voltage'.

If you send an end time in the body, and the alarm zone has a schedule to consult to find the state that it should return to, the override will only stay in effect until then.

If you do not, the override will remain in place until the next scheduled or manual change.

Optional

The ID of the alarm zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding alarm zones (such as 'Override').

Sends an override to an alarm zone to set its state to 'armed - low feel'.

If you send an end time in the body, and the alarm zone has a schedule to consult to find the state that it should return to, the override will only stay in effect until then.

If you do not, the override will remain in place until the next scheduled or manual change.

Optional

The ID of the alarm zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding alarm zones (such as 'Override').

Cancels an override, returning the alarm zone to its scheduled state.

The ID of the alarm zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding alarm zones (such as 'Override').

See the
                    item status topic for how to use the updates APIs.

Note that this API call is good for watching one item only; if you want to monitor several, you are better off with a
                    status subscription.

Follow the 'updates' field in an alarm zone
                    summary or
                    details pages to get here.

The ID of the alarm zone.

Returns these fields in the update, instead of the default set. Note that removing fields also saves you from updates to those fields.

Success. The
                        introduction describes the three status fields.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent an alarm zone, or the operator does not have a privilege on the zone's division that allows viewing alarm zones, such as 'View Site', 'Edit Site', or 'Override'."

### Use case: listing day categories

A day category links a calendar to a schedule. The calendar determines the days of the year that fall into a day category, and the schedule determines what happens at certain times on those days.

The method in this group gives you the day category hrefs you need when creating and modifying schedules.

Day categories are new to the 8.50 API.

- GET /api.

- Follow the link at features.dayCategories.dayCategories.href, appending a search term such as name=substring or name="full name" to filter the selection, and fields to tell the server what to return about each day category.

- Search the results for the day category you are after. Normally you would follow the next link until there isn't one, but day categories are not that numerous so they will most likely to fit on one page. Especially if you set top=1000 (which is advised).

This returns the day categories that match your search criteria.

The result will contain no more than 100 or 1000 (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch. Generally a site does not have too many day categories, so if you set top=1000 you are bound to collect them all.

If your result set is empty it means your operator does not have any of the privileges that allow viewing day categories, such as 'View Site', 'Configure Site', or 'Edit Schedules'. Because day categories do not have divisions, having one of those privileges in
                    any division is enough.

When you have seen them all there will be no next link.

This does not take a division query parameter because day categories are not in divisions.

Take this URL from the 'href' field in the features.dayCategories.dayCategories section of /api.

Added in 8.50.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the field names in the schema definitions in this document. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

The site does not have the RESTCardholders, RESTStatus, or RESTOverrides licence.

### Status flags

### Use cases

#### Door flag rules

#### Listing Doors

#### Opening a Door

#### Finding a door's status

These methods give you read access to basic data about Doors in the Command Centre database, and let you open them.

The first use case below introduces the main entry point. It is a paginated search interface that gives you any number of doors, each containing the fields you ask for in the query (including, for example, the URLs to open them).

If the door is online, its statusFlags field will contain one or more of these flags:

secure and free do not change when the door opens. They are about whether the door is enforcing access control, not about the current state of the door hardware.

When allowing passage, a door normally moves from closed and locked to closed and unlocked, to open and unlocked (extremely briefly), then to open and locked while someone is walking through it, then back to closed and locked. It will be secure throughout.

So, to establish if the door is in a normal state, look for 'closed' or 'open'. If neither is present, your door is in an error state.

- forced means the door was opened or unlocked while secure.

- openTooLong means the door has been open for longer than its configured DOTL time.

- tamper means one of the door's inputs is in a tampered state. The usual cause of that is a resistance moving outside nominal range, meaning the input has been cut or shorted.

- open means the door has a sensor for detecting its openness and it is reporting as such.

- closed is the inverse. The door is closed or has no open sensor.

- locked means the door locked. It reflects the state of the door's unlock sensor, if it has one. Otherwise it reflects the state of the door's unlock output, if it has that. Without an unlock output, a door is not capable of access control.

- unlocked means 'locked' is not set.

- secure means the door's normal state is closed and locked.

- free is the opposite of 'secure' -- nobody needs to badge to open it.

- If and only if the door is online, exactly one of 'closed' or 'open' will appear, and one of 'locked' or 'unlocked', and one of 'secure' or 'free'.

- GET /api.

- Follow the link at features.doors.doors.href, appending a search term such as name=substring to filter the selection if you have a lot of doors, and fields to tell the server what to return about each. The next section covers those query parameters.

- Process the results, following the next link until there isn't one.

- Find the href for the door using the process above.

- GET it.

- POST to the open
↪ URL in the commands structure of the
                  results.

- Find the href for the door using the process above, and GET it.

- Follow the updates href from that page.

- Use the flag rules above to interpret the status flags you receive.

- Follow the next link to stay up to date.

This returns a summary of the doors matching your search criteria.

The result will contain no more than 100 or 1000 doors (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have the privilege to view any doors, such as 'View Site', 'Edit Site', or 'Override - Open Door'. Perhaps there are no doors in the divisions in which your operator has privileges, or your operator has no privileges at all.

When you have loaded them all there will be no next link.

Take this URL from the 'href' field in the features.doors.doors section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

This returns the detail of one door.

Follow the 'href' field in a
                    door summary to get here.

The ID of the door.

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

Success.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent a door, or the operator does not have a privilege on the door's division that allows viewing them, such as 'View Site', 'Edit Site', or 'Override - Open Door'."

Sends an override to unlock a door.

The ID of the door.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding doors (such as 'Override - Open Door').

See the
                    item status topic for how to use the updates APIs.

Note that this API call is good for watching one item only; if you want to monitor several, you are better off with a
                    status subscription.

Follow the 'updates' field in a door
                    summary or
                    details pages to get here.

The ID of the door.

Returns these fields in the update, instead of the default set. Note that removing fields also saves you from updates to those fields.

Success. See the
                        introduction for a description of the three status fields.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent a door, or the operator does not have a privilege on the door's division that allows viewing them, such as 'View Site', 'Edit Site', or 'Override - Open Door'."

### Use cases

### Choosing and setting a cardholder's default floor

#### Listing elevator groups and their access zones

#### Setting a cardholder's default floor

These methods give you read access to Elevator Groups.

The reason you would want that is to allocate default floors to cardholders. Each cardholder can have one default floor per elevator group, so that when they badge into that group's lobby area the elevator system can arrange a car to take them to their favourite floor.

Each elevator group only goes to certain floors, so to give a cardholder a default floor you need to see which floors each elevator group services.

Command Centre represents floors with access zones. If an elevator car has two doors, front and rear, it may service two access zones on the same physical floor. A cardholder could pick either of those access zones as their default for that elevator group.

The main entry point is a paginated search that returns what you need to pick default floors for a cardholder, limited by the privilege that enables that operation on a cardholder. That is the call that you are most likely to need, but there is another that gives you all elevator groups that your operator can view, rather than the smaller set of groups your operator can use in a cardholder edit.

Your client will first need to list all elevator groups, and the floors and access zones on those elevator groups, so that it can pick from them (if knows them by name already) or present a list and allow a user to pick one (if it is interactive).

Then it will need to send a PATCH back to the cardholder to set his or her default floor for an elevator group.

- GET /api.

- Follow the link at features.cardholders.modifyPassengerDetails.href, appending a search term such as name=substring to filter the selection if you have a lot of elevator groups.

- Find the elevator group you are after, following the next link if you have lots of elevator groups.

- Look in the floorAccess array for the floor names and access zone names you can use for picking the floor, and the access zone hrefs to use in the
                  PATCH coming up.

- Find the href for the cardholder.

- PATCH it with a
                  request body containing the hrefs of the elevator groups and access zones you wish to set as that cardholder's defaults.

This searches the elevator groups that your privileges allow you to use in cardholders' default floor and passenger type assignments, returning everything you need to make those assignments.

Unlike most of the other calls in this document, it requires the RESTCardholders licence.

The result will contain no more than 100 or 1000 depending on your version, or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have the privilege to assign elevator groups to cardholders ('Modify Passenger Details'). Perhaps there are no elevator groups in the divisions in which your operator has that privilege.

When you have loaded them all there will be no next link.

Take this URL from the href field in the features.cardholders.modifyDefaultFloors section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. An array of [elevator group(#definition-Elevator-Group-Floor-Access-detail) objects and a next link for more.

See the note in the description about privileges if your result set is empty.

The site does not have the RESTCardholders licence.

This returns the name and href of the elevator groups matching your search criteria. This uses a different privilege from the modify_default_floors call, so it may not return you the groups you need. If your goal is to set cardholders' default floors, you should that call instead.

The result will contain no more than 100 or 1000 (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have the privilege to view any elevator groups, such as 'View Site' or 'Edit Site'. Perhaps there are no elevator groups in the divisions in which your operator has privileges, or your operator has no privileges at all.

When you have loaded them all there will be no next link.

Take this URL from the href field in the features.elevators.elevatorGroups section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. An array of
                        elevator group objects and a next link for more.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

This returns the detail of one elevator group.

If you are setting cardholders' default floors, you should be using the modify_default_floors call rather than this one.

The ID of the elevator group.

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

Success.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent an elevator group, or the operator does not have a privilege on the elevator group's division that allows viewing them, such as 'View Site' or 'Edit Site'.

### Status flags

### Use cases

#### Fence Zone flag rules

#### Searching for fence zones by name

#### Overriding a fence zone

#### Finding a fence zone's status

These methods give you read access to Fence Zones in the Command Centre database, and let you turn them on and off, change their modes, and shunt them.

The first use case below introduces the main entry point. It is a paginated search interface that gives you any number of fence zones, each containing the fields you ask for in the query.

If the fence zone is online, its statusFlags field may contain one or more of these flags:

- notPolled means the fence zone is shunted, which means intentionally ignored, and is essentially offline.

- overridden means just that.

- deterrentUnknown means the controller cannot determine the state of the fence zone. This happens when there is a cabling problem between the C6000 and the fence controller (energiser).

- on means the fence zone is live, energised. Take care.

- off means the fence zone is off.

- lowFeel means the fence energiser is delivering enough voltage to detect disturbances. Depending on the fence zone's configuration, it may also be a deterrant.

- highVoltage means the pulse is delivering a deterrant pulse.

- hVPlusMode means the fence voltage has increased in response to a disturbance. After some time or an override it will leave this mode. See the description of HVPlus mode in the Configuration Client's online help.

- serviceMode means a technician has manually forced service mode at the fence controller. This safety measure prevents a Command Centre operator energising a hardware technician.

- voltageKnown means Command Centre has the current voltage for the zone, provided there has been at least one pulse from the energiser recently. This can only happen when the zone is on, obviously. The 'voltage' field will contain the voltage at the last pulse.

- alert means the fence voltage is outside the alert range. Typically this means an electical problem.

- warning means the fence voltage is between the alert and warning ranges. Typically this means it has grounded somewhere.

- preArm means the zone is performing its pre-arm check.

- lockedOut means the zone has been locked out at a keypad.

- parentAlert means there is a problem with the fence controller.

- If and only if the fence zone is online, there will be exactly one of 'deterrentUnknown', 'on', or 'off'. That is your test for whether a fence zone is in error.

- If 'on', there will be exactly one of 'lowFeel', 'highVoltage', or 'hVPlusMode'.

- If 'off', there will be exactly one of 'lowFeel' or 'highVoltage'.

- 'hVPlusMode' will only appear if the fence zone is on.

- If there is 'alert' there will never be 'warning'.

- None of the flags above will appear if the fence zone is offline and only 'notPolled' will appear if it is not polled.

- The 'voltage' field only contains a valid value if you receive 'voltageKnown' and the energiser has pulsed at least once since the zone turned on.

- GET /api.

- Follow the link at features.fenceZones.fenceZones.href
↪, appending a search term such as name=substring to filter the fence zones, and fields to tell the server what to return about each. The next section covers those query parameters.

- Process the results, following the next link until there isn't one.

- Find the href for the fence zone using the process above.

- GET it.

- Find the API URL you require in the commands structure of the results, such as off, highVoltage, or shunt, from
                  the detail.

- POST to that URL. You do not need to send anything in the body of the POST.

- Find the href for the fence zone using the process above, and GET it.

- Take the updates
↪ href from that page. If you are after the fence's voltage and are using version 8.00, append fields=defaults,voltage (after a ? or &). You do not need that for later versions as voltage became a default field in 8.10.

- GET it.

- Use the flag rules above to interpret the status flags you receive.

- Follow the next link to stay up to date.

This returns a summary of the fence zones matching your search criteria.

The result will contain no more than 100 or 1000 fence zones (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have a privilege that allows viewing fence zones, such as 'View Site', 'Edit Site', or 'Maintenance Override'. Perhaps there are no fence zones in the divisions in which your operator has privileges, or your operator has no privileges at all.

When you have loaded them all there will be no next link.

Take this URL from the 'href' field in the features.fenceZones.fenceZones section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

This returns the detail of one fence zone.

Follow the 'href' field in an
                    fence zone summary to get here.

The ID of the fence zone.

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

Success.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent a fence zone, or the operator does not have a privilege on the zone's division that allows viewing fence zones, such as 'View Site', 'Edit Site', or 'Maintenance Override'."

Sends an override to an alarm zone to turn it on until the next scheduled or manual change.

The ID of the fence zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding fence zones (such as 'Override', or 'Maintenance Override' for shunts).

Sends an override to an alarm zone to turn it off until the next scheduled or manual change.

The ID of the fence zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding fence zones (such as 'Override', or 'Maintenance Override' for shunts).

Sends an override to an alarm zone to shunt it, effectively preventing all communication with it.

The ID of the fence zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding fence zones (such as 'Override', or 'Maintenance Override' for shunts).

Sends an override to an alarm zone to unshunt it, re-enabling its communication.

The ID of the fence zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding fence zones (such as 'Override', or 'Maintenance Override' for shunts).

Sends an override to an alarm zone to change it to high voltage mode until the next scheduled or manual change.

The ID of the fence zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding fence zones (such as 'Override', or 'Maintenance Override' for shunts).

Sends an override to an alarm zone to change it to 'low feel' mode until the next scheduled or manual change.

The ID of the fence zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding fence zones (such as 'Override', or 'Maintenance Override' for shunts).

Cancels an active override.

The ID of the fence zone.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding fence zones (such as 'Override', or 'Maintenance Override' for shunts).

See the
                    item status topic for how to use the updates APIs.

Note that this API call is good for watching one item only; if you want to monitor several, you are better off with a
                    status subscription.

Follow the 'updates' field in a door
                    summary or
                    details pages to get here.

The ID of the fence zone.

Returns these fields in the update, instead of the default set. Note that removing fields also saves you from updates to those fields.

Success. See the
                        introduction for a description of the three status fields and the
                        detail page for voltage.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent a fence zone, or the operator does not have a privilege on the zone's division that allows viewing fence zones, such as 'View Site', 'Edit Site', or 'Maintenance Override'."

### Status flags

### Use cases

#### Input flag rules

#### Listing Inputs

#### Overriding an Input

#### Finding an input's status

These methods, introduced in v8.10, give you read and override access to Input items.

The first use case below introduces the main entry point. It is a paginated search interface that gives you any number of inputs, each containing the fields you ask for in the query. You can, for instance, ask for the URLs you need to shunt or isolate an input.

Even though the Configuration client lets you set your own state names for open, closed, tampered open, and tampered short, status flags will always use 'closed' or 'open', and possibly 'tamper'.

Two-state inputs cannot report a tamper, because it is impossible to detect. They can only be open or closed. Hence the name.

Three-state inputs can report a tamper, but only one of tampered open or tampered closed, depending on their end-of-line resistance settings.

Four-state inputs can report tampered open (an open circuit) or tampered closed (a short).

If the input is online, its statusFlags field may contain one or more of these flags:

closed means the input circuit is closed.

open means the input circuit is open.

tamper means the input circuit is shorted or open.

notPolled means the input is shunted, which means intentionally ignored, and is essentially offline.

isolated means the input state will not prevent arming an alarm zone.

If and only if the input is online and not shunted ('notPolled' status flag), exactly one of 'closed' or 'open' will appear.

'closed' or 'open' may still appear if the input is tampered.

So, to establish if the input is in a completely normal state, look for 'closed' or 'open', and make sure 'tamper' is
                not there. However bear in mind that inputs are often shunted for ordinary reasons.

- closed means the input circuit is closed.

- open means the input circuit is open.

- tamper means the input circuit is shorted or open.

- notPolled means the input is shunted, which means intentionally ignored, and is essentially offline.

- isolated means the input state will not prevent arming an alarm zone.

- If and only if the input is online and not shunted ('notPolled' status flag), exactly one of 'closed' or 'open' will appear.

- 'closed' or 'open' may still appear if the input is tampered.

- GET /api.

- Follow the link at features.inputs.inputs.href, appending a search term such as name=substring to select the inputs, top if you expect lots of them, and fields to tell the server what to return about each. The next section covers those query parameters.

- Process the results, following the next link if there is one.

- Find the href for the input using the process above.

- GET it.

- Find the API URL for the override you need in the commands structure of the
                  results.

- POST to that URL with an empty body.

- Find the href for the input using the process above, and GET it.

- Take the updates
↪ href from that page.

- GET it.

- Use the flag rules above to interpret the status flags you receive.

- Follow the next link to stay up to date.

This returns a summary of the inputs matching your search criteria.

The result will contain no more than 100 or 1000 inputs (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have the privilege to view any inputs, such as 'View Site', 'Edit Site', or 'Maintenance Override'. Perhaps there are no inputs in the divisions in which your operator has privileges, or your operator has no privileges at all.

When you have loaded them all there will be no next link.

Take this URL from the 'href' field in the features.inputs.inputs section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

This returns the detail of one input.

Follow the 'href' field in an
                    input summary to get here.

The ID of the input.

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

Success.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent an input, or the operator does not have a privilege on the input's division that allows viewing inputs, such as 'View Site', 'Edit Site', or 'Maintenance Override'."

Sends an override to shunt an input, preventing all communication.

The ID of the input.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have the 'Maintenance Override' privilege.

Sends an override to unshunt an input, re-enabling communication.

The ID of the input.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have the 'Maintenance Override' privilege.

Sends an override to isolate an input. An isolated input will not prevent an alarm zone from arming.

The ID of the input.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have the 'Maintenance Override' privilege.

Sends an override to end the isolation of an input.

The ID of the input.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have the 'Maintenance Override' privilege.

See the
                    item status topic for how to use the updates APIs.

Note that this API call is good for watching one item only; if you want to monitor several, you are better off with a
                    status subscription.

Follow the 'updates' field in an input
                    summary or
                    details pages to get here.

The ID of the input.

Returns these fields in the update, instead of the default set. Note that removing fields also saves you from updates to those fields.

Success. See the
                        introduction for a description of the three status fields.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent an input, or the operator does not have a privilege on the input's division that allows viewing inputs, such as 'View Site', 'Edit Site', or 'Maintenance Override'."

### Status flags

### Use cases

#### Listing Interlock Groups

#### Finding the status of many items including an interlock group

#### Finding the status of an interlock group

#### Overriding an Interlock Group

These methods, yet to be released, will give you read access to Interlock Group items.

API support for interlocks is still in development and may change in future versions.

The first use case below introduces the main entry point. It is a paginated search interface that gives you any number of interlock groups, each containing the fields you ask for in the query. You can, for instance, ask for the IDs you need to monitor their status.

If an interlock is not in an error state it will return one flag out of the following set:

secure means the interlock items are closed and the door/s will open to a badge. This is probably where an interlock group spends most of its time.

open means the interlock's doors will not open because at least one of the other items in the group already is. This is a normal state after someone gains access at an interlock door. It will last until the door closes.

overridden means the interlock group has received a disable override. Doors will open as normal.

forced means that the interlock rules have been breached. This could be because someone forced a door, or used an emergency release while another door was already open.

If an interlock is completely normal it will report 'secure' or 'open'.

- secure means the interlock items are closed and the door/s will open to a badge. This is probably where an interlock group spends most of its time.

- open means the interlock's doors will not open because at least one of the other items in the group already is. This is a normal state after someone gains access at an interlock door. It will last until the door closes.

- overridden means the interlock group has received a disable override. Doors will open as normal.

- forced means that the interlock rules have been breached. This could be because someone forced a door, or used an emergency release while another door was already open.

- GET /api.

- Follow the link at features.interlockGroups.interlockGroups.href, appending a search term such as name=substring to select the interlocks, top if you expect lots of them, and fields to tell the server what to return about each. The next section covers those query parameters.

- Process the results, following the next link if there is one.

- Find the IDs of all the items you're interested in, including the interlock group, by searching for them with a query parameter appended such as fields=name,id.

- Create a
                  status subscription for those items.

- Find the href for the interlock using the process above, and GET it.

- Take the updates href from that page.

- GET it.

- Use the flag rules above to interpret the status flags you receive.

- Follow the next link to stay up to date.

- Find the href for the interlock using the process above.

- GET it.

- Find the API URL for the override you need (disable or re-enable) in the commands structure of the
                  results.

- POST to that URL with an empty body.

Not yet available. API support for interlocks is still in development and may change in future versions.

This returns a summary of the interlock groups matching your search criteria.

The result will contain no more than 100 or 1000 interlock groups (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have a privilege that allows viewing interlock groups, such as 'View Site', 'Edit Site', or 'Maintenance Override'. Perhaps there are no interlock groups in the divisions in which your operator has privileges, or your operator has no privileges at all.

When you have loaded them all there will be no next link.

Take this URL from the 'href' field in the features.interlockGroups.interlockGroups section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

The site has neither the RESTStatus nor the RESTOverrides licence.

Not yet available. API support for interlocks is still in development and may change in future versions.

This returns the detail of one interlock group.

Follow the 'href' field in an
                    interlock group summary to get here.

The ID of the interlock group.

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

Success.

The site has neither the RESTStatus nor the RESTOverrides licence.

The request's URL does not represent an interlock or the operator does not have a privilege on the interlock's division that allows viewing interlocks, such as 'View Site', 'Edit Site', or 'Override'."

Not yet available. API support for interlocks is still in development and may change in future versions.

Sends an override to disable an interlock group, allowing all doors to act independently.

The ID of the interlock group.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have the 'Override' privilege.

Not yet available. API support for interlocks is still in development and may change in future versions.

Cancels the disabling override on an interlock group, causing the doors to return to interlocking behaviour.

The ID of the interlock group.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have the 'Override' privilege.

Not yet available. API support for interlocks is still in development and may change in future versions.

See the
                    item status topic for how to use the updates APIs.

Note that this API call is good for watching one item only; if you want to monitor several, you are better off with a
                    status subscription.

Follow the 'updates' field in an interlock group
                    summary or
                    details pages to get here.

The ID of the interlock group.

Returns these fields in the update, instead of the default set. Note that removing fields also saves you from updates to those fields.

Success. See the
                        introduction for a description of the status fields.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent an interlock or the operator does not have a privilege on the interlock's division that allows viewing interlocks, such as 'View Site', 'Edit Site', or 'Override'."

### Overrides

### Use cases

#### Listing Macros

#### Running a Macro

These methods give you read access to basic data about the Macros in the Command Centre database, and let you run them.

Reading the section 'Understanding Macros' in the Configuration client help is an excellent way to do exactly that.

The first use case below introduces the main entry point. It is a paginated search interface that gives you any number of macros, each containing the fields you ask for in the query, including (for example) the URL you need to run the macro.

You cannot use the REST API to change a macro's schedule. You must use the Command Centre or Configuration client for that.

- GET /api.

- Follow the link at features.macros.macros.href
↪, appending a search term (described below) to narrow the results if your installation has a lot of macros.

- Process the results, following the next link until there isn't one.

- Find the href for the macro using the process above.

- GET it.

- If your operator is able to run the macro, the results will contain a URL at commands.run.href.
                  POST to that to run the macro - no body required.

This returns a summary of the macros matching your search criteria.

The result will contain no more than 100 or 1000 macros (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have the privilege to view any macros, such as 'View Site', 'Run Macros', or 'Schedule and Run Macros'. Perhaps there are no macros in the divisions in which your operator has privileges, or your operator has no privileges at all.

When you have loaded them all there will be no next link.

Take this URL from the 'href' field in the features.macros.macros section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

This returns the detail of one macro.

Follow the 'href' field in an
                    macro summary to get here.

The ID of the macro.

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

Success.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent a macro, or the operator does not have a privilege on the macro's division that allows viewing it, such as 'View Site', 'Run Macros', or 'Schedule and Run Macros'.

Sends a run request to a macro.

The ID of the macro.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows running macros ('Run Macro' or 'Schedule and Run Macro', unsurprisingly).

### Override times

### Overrides always use 'on' and 'off'

### Status flags

### Use cases

#### Output flag rules

#### Listing Outputs

#### Switching an Output

#### Finding an output's status

These methods give you read access to Outputs in the Command Centre database, and let you override them.

The first use case below introduces the main entry point. It is a paginated search interface that gives you any number of outputs, each containing the fields you ask for in the query, including (for example) the URLs you need to switch the outputs on and off.

The Configuration client allows you to assign different display strings to the two normal output states, on and off. On could be 'green', for example, and off could be 'red'. Regardless, the overrides you apply to an output are called 'on' and 'off'.

Status flags, on the other hand, use the language of the relays on the hardware modules. They will report 'closed' for an output that is on, and 'open' for one that is off.

If the output is online, its statusFlags field may contain one or more of these flags:

relayStateUnknown means the controller does not know what the output should be doing.

closed means the output relay is closed.

open means the output relay is open.

pulsed means the relay's change in state is momentary.

switchingDisabled means switching this output is disabled.

overridden can appear whether the output is online or offline. It means the output has an override in effect.

- End-times on overrides are not accurate to the second. Internally, Command Centre converts the end time to a duration, so you may find that submitting end times in the very near future does not have the exact effect you expect.

- The end time you set for an override cannot be in the past or more than 24 hours into the future.

- relayStateUnknown means the controller does not know what the output should be doing.

- closed means the output relay is closed.

- open means the output relay is open.

- pulsed means the relay's change in state is momentary.

- switchingDisabled means switching this output is disabled.

- overridden can appear whether the output is online or offline. It means the output has an override in effect.

- If and only if the output is online, one of 'relayStateUnknown', 'closed', or 'open' will appear. That is your test for whether an output is in error.

- Of the above, only 'overridden' can appear when the output is offline.

- GET /api.

- Follow the link at features.outputs.outputs.href
↪, appending a search term such as name=substring to select the outputs, and fields to tell the server what to return about each. The next section covers those query parameters.

- Process the results, following the next link until there isn't one.

- Find the href for the output using the process above.

- GET it.

- Look in the commands structure of the
                  results to find the API URLs that turn the output on, off, or cancel a previous override. Use the until variants if you want to specify an end time.

- POST to that URL. Those with until in their command block keys require a JSON object in the body; the others expect it empty.

- Find the href for the output using the process above, and GET it.

- Take the updates
↪ href from that page.

- GET it.

- Use the flag rules above to interpret the status flags you receive.

- Follow the next link to stay up to date.

This returns a summary of the outputs matching your search criteria.

The result will contain no more than 100 or 1000 outputs (depending on your version), or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have the privilege to view any outputs, such as 'View Site', 'Edit Site', or 'Override'. Perhaps there are no outputs in the divisions in which your operator has privileges, or your operator has no privileges at all.

When you have loaded them all there will be no next link.

Take this URL from the 'href' field in the features.outputs.outputs section of /api.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

This returns the detail of one output.

Follow the 'href' field in an
                    output summary to get here.

The ID of the output.

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

In v8.00 you will receive the href and internal ID even if you did not ask for them. In 8.10 and later you will only get what you asked for. If you are going to send the fields parameter and need the href or ID, be explicit.

Success.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent an output, or the operator does not have a privilege on the output's division that allows viewing outputs, such as 'View Site', 'Edit Site', or 'Override'."

Sends an override to close an output.

If you send an end time in the body, the override will only stay in effect until then.

Optional

The ID of the output.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding outputs (such as 'Override', or 'Maintenance Override' for shunts).

Sends an override to pulse an output.

Pulsing an output differs from turning it on in two ways:

You cannot specify a duration for it to stay activated, because that comes from the output's configuration.

A pulsed output will stay on for its pulse time even if another event seeks to deactivate it (an 'off' override will still deactivate the output).

Added in 8.50.

- You cannot specify a duration for it to stay activated, because that comes from the output's configuration.

- A pulsed output will stay on for its pulse time even if another event seeks to deactivate it (an 'off' override will still deactivate the output).

The ID of the output.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The output is not configured for pulsing (8.70 and later only).

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding outputs (such as 'Override', or 'Maintenance Override' for shunts).

Sends an override to open an output.

If you send an end time in the body, the override will only stay in effect until then.

Optional

The ID of the output.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The server could not parse the POST parameters. There could be a syntax error in your JSON.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding outputs (such as 'Override', or 'Maintenance Override' for shunts).

Cancels an override, returning the output to its previous state.

The ID of the output.

Attributes this override to the cardholder with this ID rather than the REST operator. Privilege checks will use the operator as normal, but event monitors and reports will state that the person responsible for the override was the attributed cardholder, not the REST operator. The REST operator will appear in a special mention in the event's details.

First available in 8.70. Versions older than 8.70 will ignore the parameter, leaving the override attributed to the REST operator.

Success.

The site does not have the RESTOverrides licence (in which case the body of the result will say so), or the operator does not have a privilege that allows overriding outputs (such as 'Override', or 'Maintenance Override' for shunts).

See the
                    item status topic for how to use the updates APIs.

Note that this API call is good for watching one item only; if you want to monitor several, you are better off with a
                    status subscription.

Follow the 'updates' field in an output
                    summary or
                    details pages to get here.

The ID of the output.

Returns these fields in the update, instead of the default set. Note that removing fields also saves you from updates to those fields.

Success. See the
                        introduction for a description of the three status fields.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent an output, or the operator does not have a privilege on the output's division that allows viewing outputs, such as 'View Site', 'Edit Site', or 'Override'."

These methods give you read/write access to seven types of schedules. Avigilon Engage schedules are not included.

The main entry point is a paginated search that gives you any number of schedules. The most useful field on a schedule is a list of state changes and the days and times that those changes should occur.

There is also a method that allows creating new schedules.

Schedules are new to 8.50.

Licensing

The schedules APIs are licensed a little differently from most others. RESTCardholders gives you Cardholder Access Schedules, while RESTStatus and RESTOverrides give you all schedule types.

This returns a summary of the schedules matching your search criteria.

The result will contain no more than 100 or 1000, depending on your version, or as many as you asked for more in your request; you should follow the next link, if it is present, to collect the next batch.

If your result set is empty it means your operator does not have the privilege to view schedules, such as 'View Schedules', 'Edit Schedules', or 'Schedule Access Zone'. Perhaps there are no schedules in the divisions in which your operator has privileges, or your operator has no privileges at all.

Note that the privilege 'Schedule Access Zone' only lets you see Access Zone schedules, not the other five types.

When you have loaded them all there will be no next link.

Take this URL from the 'href' field in the features.schedules.schedules section of /api.

Added in 8.50.

Changes the sort field between database ID and name.

If you prefix id or name with a minus sign (ASCII 45), the sort order is reversed.

There are two very strong reasons to sort by ID:

We
                        strongly recommend sorting by ID. In case you were still in doubt, we will do that by default in a future version of Command Centre.

Limits the results to no more than this many items per page.

Older versions of Command Centre returned 100 items per page. That is acceptable for GUI applications that will only display the first page, but for integrations that intend to proceed through the entire database it causes a lot of chatter.

8.70 and later versions will return 1000 items per request. This is about where a graph of performance versus page size begins to level out.

Limits the returned items to those with a name that matches this string. Without surrounding quotes or a percent sign or underscore, it is a substring match; surround the parameter with double quotes "..." for an exact match. Without quotes, a percent sign % will match any substring and an underscore will match any single character.

The search is always case-insensitive. Results are undefined if you do a substring search for the empty string (name=). You will receive no items if you search for those with no name (name=""), as all items must have a name.

Search parameters are ANDed together.

Limits the returned items to those that are in these divisions.

That includes all the items in those divisions' child divisions, because Command Centre treats items as though they are also in their division's parent, and its parent, and so on up to the root division.

List the IDs of the divisions you are interested in separated by commas. Item IDs are short alphanumeric strings, not URLs.

Results are undefined if you provide an ID that is not in the form of a division ID.

Search parameters are ANDed together.

Limits the returned items to those with a description that matches this string. By default it is a substring match; surround it with double quotes "..." for an exact match. A _ will match any single character, and a % will match any substring. With or without quotes, having either of these wildcards in the string will anchor it at both ends as though you had surrounded it with ".

The search is always case-insensitive. Results are undefined if you search for the empty string (description= or description="").

Search parameters are ANDed together.

Return these fields in the search results. The values you can list are the same as the field names in the details page. Using this you can return everything on the summary page that you would find on the details page. Separate values with commas.

Use the special value defaults to return the fields you would have received had you not given the parameter at all. Obviously only do that if you have more to add.

Treat the string matches as case-sensitive.

- Sorting by name carries a risk of missing or duplicating objects if your result set spans multiple pages and another operator is editing the database while your REST client is enumerating them. Sorting by ID does not carry that risk.

- Following a next link is
                          dramatically quicker when sorting by ID.

Success. See the note in the description about privileges if your result set is empty.

The site does not have the RESTStatus, RESTOverrides, or RESTCardholders licence.

This is how you create a new schedule.

Your POST needs a body containing JSON in the same from received from a GET, containing a division and type, and (if you want it to be useful) dayCategories.

Take this URL from the 'href' field in the features.schedules.schedules section of /api.

Added in 8.50.

The only fields you must supply in a POST are division and type. The server can make up a name for you, and is happy to leave the timetable empty.

Success.

The parameters are invalid. Check the body of the response: it may be helpful.

If you see 'No schedule found', the server could not parse the JSON in the body of your POST. Remember that the state field is an array.

The operator does not have a privilege that allows creating schedules, or the site does not have any of the RESTCardholders, RESTOverrides, RESTStatus, or (after 9.00) RESTConfiguration licences.

This returns the detail of one schedule.

Follow the 'href' field in an
                    schedule summary to get here.

The ID of the schedule.

Return these fields in the details page instead of the default set. The values you can list are the same as the field names you would see in the results. Use it to cut back on the size of the response. Separate values with commas.

Treat the string matches as case-sensitive.

Success.

A server running 8.50 or earlier is missing the RESTStatus licence. A server running 8.60 or later is missing both the RESTStatus and RESTOverrides licence.

The request's URL does not represent a schedule, or the operator does not have a privilege on the schedule's division that allows viewing schedules, such as 'View Schedules', 'Edit Schedules', or 'Schedule Access Zone'."

Modifies a schedule according to the body of the PATCH.

Added in 8.50.

You do not need to supply any fields in the body of this PATCH, but if you want it to achieve something you should add at least one. Probably dayCategories.

Success. The response body will contain feedback from the server about your PATCH.

Success.

The parameters are invalid. Check the body of the response: it may be helpful.

If you see 'No schedule found', the server could not parse the JSON in the body of your request. Remember that the state field is an array.

The operator does not have a privilege that allows modifying that schedule, or the site does not have any of the RESTCardholders, RESTOverrides, RESTStatus, or (after 9.00) RESTConfiguration licences.

The request's URL does not represent a schedule, or the operator does not have a privilege on the schedule's division that allows viewing schedules, such as 'View Schedules', 'Edit Schedules', or 'Schedule Access Zone'."

Deletes the schedule identified by the request's URL.

Added in 8.50.

Success.

You cannot delete a schedule that is in use. The body of the 400 response will tell you if that is the case. One way to find out which items are holding you up is to GET the same URL and look in the scheduledItems block.

The operator does not have a privilege that allows deleting that schedule ('Edit Schedules'), or the site does not have any of the RESTCardholders, RESTOverrides, RESTStatus, or (after 9.00) RESTConfiguration licences.

The request's URL does not represent a schedule, or the operator does not have a privilege on the schedule's division that allows viewing schedules, such as 'View Schedules', 'Edit Schedules', or 'Schedule Access Zone'."

An array of access zone summaries, and a next link for more.

An array of Access Zone summaries.

The link to the next page. Absent if you have retrieved them all.

**results:


Access Zone summary:** An array of Access Zone summaries.

**Access Zone summary:** The link to the next page. Absent if you have retrieved them all.

An array of access zones, and a link to a special 'outside the system' access zone. This is the list of access zones into which your operator has the privilege to move cardholders.

An array of Access Zone summaries, just as you would receive from the Access Zone search GET.

This contains the href you should use to move a cardholder out of all Access Zones.

The link to the next page. Absent if you have retrieved them all.

**results:


Access Zone summary:** An array of Access Zone summaries, just as you would receive from the Access Zone search GET.

**Access Zone summary:** This contains the href you should use to move a cardholder out of all Access Zones.

**outsideOfSystem:
object:** The link to the next page. Absent if you have retrieved them all.

/api/access_zones returns an array of these. It is a subset of what you get from a access zone's detail page at /api/access_zones/{id} (linked as the href in this object).

A link to an
                        access zone detail object for this access zone. This is Command Centre's identifier for this access zone: use it whenever you need to specify an access zone in REST operations.

An alphanumeric identifier, unique to the server.

This is the ID to use in the source parameter of
                        event filters if you want to limit your events to particular access zones.

**href:
string
(url):** A link to an
                        access zone detail object for this access zone. This is Command Centre's identifier for this access zone: use it whenever you need to specify an access zone in REST operations.

**id:
string:** An alphanumeric identifier, unique to the server.
This is the ID to use in the source parameter of
                        event filters if you want to limit your events to particular access zones.

/api/access_zones/{id} returns one of these.

As well as the properties below, it contain a block called doors. This is reserved for future development and its behaviour could change in later versions of Command Centre.

The division containing this Access Zone.

A list containing names of and links to the doors that control entry to this access zone.

This is the link to the
                                      door's detail (which contains a return link back to this page).

The number of cardholders in the zone, according to its zone counting configuration.

An access zone's count is part of its state, so
                            all that reading applies here too. The authoritative source is the zone's hardware controller, so the REST server only has it if it is monitoring it for another reason. If it is not, it returns a zero.

That is why zone counts are not in the default set of fields. You can ask for them using the 'fields' parameter, but you risk receiving a zero, so the recommended way is to follow the Access Zone's 'updates' link with fields=defaults,zoneCount appended after the appropriate query parameter separator.

A zone count is correct iff the access zone is online: the status flags must contain one of 'secure', 'dualAuth', 'codeOrCard', or 'free'. Even then, the zone count could be a minute out of date (depending on a server property that determines how long a hardware item can be silent before the server calls it offline).

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

Short names are not displayed by default. You must ask for them using the 'fields' parameter:

?fields=shortname,....

Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.

Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

This block describes this item's hardware controller.

Retrieving it takes a little more time than the other fields so only ask for it if you need it.

Added in 8.50.

This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the access zone, changing its state.

It will be missing if your operator does not have a privilege that allows overriding the access zone (examples of which are in the documentation for the POSTs).

POST to this to override the zone into 'free - no PIN' access mode until the next scheduled change.

POST to this to override the zone into 'free - no PIN' access mode for a fixed time.

POST to this to override the zone into 'free - PIN' access mode until the next scheduled change.

POST to this to override the zone into 'free - PIN' access mode for a fixed time.

POST to this to override the zone into 'secure - no PIN' access mode until the next scheduled change.

POST to this to override the zone into 'secure - no PIN' access mode for a fixed time.

POST to this to override the zone into 'secure - PIN' access mode until the next scheduled change.

POST to this to override the zone into 'secure - PIN' access mode for a fixed time.

POST to this to override the zone into 'code or card - no PIN' access mode until the next scheduled change.

POST to this to override the zone into 'code or card - no PIN' access mode for a fixed time.

POST to this to override the zone into 'code or card - PIN' access mode until the next scheduled change.

POST to this to override the zone into 'code or card - PIN' access mode for a fixed time.

POST to this to override the zone into 'dual auth - no PIN' access mode until the next scheduled change.

POST to this to override the zone into 'dual auth - no PIN' access mode for a fixed time.

POST to this to override the zone into 'dual auth - PIN' access mode until the next scheduled change.

POST to this to override the zone into 'dual auth - PIN' access mode for a fixed time.

POST to this to forgive anti-passback for all cardholders in the zone.

POST to this set the zone's cardholder count.

POST to this set the zone into lockdown mode.

POST to this cancel lockdown on the zone, returning it to its scheduled mode.

POST to this cancel an active override on the zone, returning it to its scheduled mode. This will not affect a lockdown.

**description:
string:** The division containing this Access Zone.

**division:
object:** A list containing names of and links to the doors that control entry to this access zone.

**doors:
object[]:** This is the link to the
                                      door's detail (which contains a return link back to this page).

**object





name:
string



href:
string
(url)



This is the link to the
                                      door's detail (which contains a return link back to this page).:** The number of cardholders in the zone, according to its zone counting configuration.
An access zone's count is part of its state, so
                            all that reading applies here too. The authoritative source is the zone's hardware controller, so the REST server only has it if it is monitoring it for another reason. If it is not, it returns a zero.
That is why zone counts are not in the default set of fields. You can ask for them using the 'fields' parameter, but you risk receiving a zero, so the recommended way is to follow the Access Zone's 'updates' link with fields=defaults,zoneCount appended after the appropriate query parameter separator.
A zone count is correct iff the access zone is online: the status flags must contain one of 'secure', 'dualAuth', 'codeOrCard', or 'free'. Even then, the zone count could be a minute out of date (depending on a server property that determines how long a hardware item can be silent before the server calls it offline).

**name:
string:** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**href:
string
(url):** Short names are not displayed by default. You must ask for them using the 'fields' parameter:
?fields=shortname,....

**zoneCount:
integer:** Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.
Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

**notes:
string:** The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

**shortName:
string

 (up to 16 chars):** This block describes this item's hardware controller.
Retrieving it takes a little more time than the other fields so only ask for it if you need it.
Added in 8.50.

**updates:
object:** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**href:
string
(url):** A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the access zone, changing its state.
It will be missing if your operator does not have a privilege that allows overriding the access zone (examples of which are in the documentation for the POSTs).

**statusFlags:
string[]:** POST to this to override the zone into 'free - no PIN' access mode until the next scheduled change.

**string:** POST to this to override the zone into 'free - no PIN' access mode for a fixed time.

**connectedController:
object:** POST to this to override the zone into 'free - PIN' access mode until the next scheduled change.

**name:
string



href:
string
(url)



This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.


id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.:** POST to this to override the zone into 'free - PIN' access mode for a fixed time.

**name:
string:** POST to this to override the zone into 'secure - no PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'secure - no PIN' access mode for a fixed time.

**id:
string:** POST to this to override the zone into 'secure - PIN' access mode until the next scheduled change.

**commands:
object:** POST to this to override the zone into 'secure - PIN' access mode for a fixed time.

**free:
object






href:
string
(url)




POST to this to override the zone into 'free - no PIN' access mode until the next scheduled change.





freeUntil:
object






href:
string
(url)




POST to this to override the zone into 'free - no PIN' access mode for a fixed time.





freePin:
object






href:
string
(url)




POST to this to override the zone into 'free - PIN' access mode until the next scheduled change.





freePinUntil:
object






href:
string
(url)




POST to this to override the zone into 'free - PIN' access mode for a fixed time.





secure:
object






href:
string
(url)




POST to this to override the zone into 'secure - no PIN' access mode until the next scheduled change.





secureUntil:
object






href:
string
(url)




POST to this to override the zone into 'secure - no PIN' access mode for a fixed time.





securePin:
object






href:
string
(url)




POST to this to override the zone into 'secure - PIN' access mode until the next scheduled change.





securePinUntil:
object






href:
string
(url)




POST to this to override the zone into 'secure - PIN' access mode for a fixed time.





codeOnly:
object






href:
string
(url)




POST to this to override the zone into 'code or card - no PIN' access mode until the next scheduled change.





codeOnlyUntil:
object






href:
string
(url)




POST to this to override the zone into 'code or card - no PIN' access mode for a fixed time.





codeOnlyPin:
object






href:
string
(url)




POST to this to override the zone into 'code or card - PIN' access mode until the next scheduled change.





codeOnlyPinUntil:
object






href:
string
(url)




POST to this to override the zone into 'code or card - PIN' access mode for a fixed time.





dualAuth:
object






href:
string
(url)




POST to this to override the zone into 'dual auth - no PIN' access mode until the next scheduled change.





dualAuthUntil:
object






href:
string
(url)




POST to this to override the zone into 'dual auth - no PIN' access mode for a fixed time.





dualAuthPin:
object






href:
string
(url)




POST to this to override the zone into 'dual auth - PIN' access mode until the next scheduled change.





dualAuthPinUntil:
object






href:
string
(url)




POST to this to override the zone into 'dual auth - PIN' access mode for a fixed time.





forgiveAntiPassback:
object






href:
string
(url)




POST to this to forgive anti-passback for all cardholders in the zone.





setZoneCount:
object






href:
string
(url)




POST to this set the zone's cardholder count.





lockDown:
object






href:
string
(url)




POST to this set the zone into lockdown mode.





cancelLockDown:
object






href:
string
(url)




POST to this cancel lockdown on the zone, returning it to its scheduled mode.





cancel:
object






href:
string
(url)




POST to this cancel an active override on the zone, returning it to its scheduled mode. This will not affect a lockdown.:** POST to this to override the zone into 'code or card - no PIN' access mode until the next scheduled change.

**free:
object:** POST to this to override the zone into 'code or card - no PIN' access mode for a fixed time.

**href:
string
(url)




POST to this to override the zone into 'free - no PIN' access mode until the next scheduled change.:** POST to this to override the zone into 'code or card - PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'code or card - PIN' access mode for a fixed time.

**freeUntil:
object:** POST to this to override the zone into 'dual auth - no PIN' access mode until the next scheduled change.

**href:
string
(url)




POST to this to override the zone into 'free - no PIN' access mode for a fixed time.:** POST to this to override the zone into 'dual auth - no PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'dual auth - PIN' access mode until the next scheduled change.

**freePin:
object:** POST to this to override the zone into 'dual auth - PIN' access mode for a fixed time.

**href:
string
(url)




POST to this to override the zone into 'free - PIN' access mode until the next scheduled change.:** POST to this to forgive anti-passback for all cardholders in the zone.

**href:
string
(url):** POST to this set the zone's cardholder count.

**freePinUntil:
object:** POST to this set the zone into lockdown mode.

**href:
string
(url)




POST to this to override the zone into 'free - PIN' access mode for a fixed time.:** POST to this cancel lockdown on the zone, returning it to its scheduled mode.

**href:
string
(url):** POST to this cancel an active override on the zone, returning it to its scheduled mode. This will not affect a lockdown.

**name:
string:** This is the link to the
                                      door's detail (which contains a return link back to this page).

**name:
string:** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**free:
object:** POST to this to override the zone into 'free - no PIN' access mode until the next scheduled change.

**href:
string
(url)




POST to this to override the zone into 'free - no PIN' access mode until the next scheduled change.:** POST to this to override the zone into 'free - no PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'free - PIN' access mode until the next scheduled change.

**freeUntil:
object:** POST to this to override the zone into 'free - PIN' access mode for a fixed time.

**href:
string
(url)




POST to this to override the zone into 'free - no PIN' access mode for a fixed time.:** POST to this to override the zone into 'secure - no PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'secure - no PIN' access mode for a fixed time.

**freePin:
object:** POST to this to override the zone into 'secure - PIN' access mode until the next scheduled change.

**href:
string
(url)




POST to this to override the zone into 'free - PIN' access mode until the next scheduled change.:** POST to this to override the zone into 'secure - PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'code or card - no PIN' access mode until the next scheduled change.

**freePinUntil:
object:** POST to this to override the zone into 'code or card - no PIN' access mode for a fixed time.

**href:
string
(url)




POST to this to override the zone into 'free - PIN' access mode for a fixed time.:** POST to this to override the zone into 'code or card - PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'code or card - PIN' access mode for a fixed time.

**secure:
object:** POST to this to override the zone into 'dual auth - no PIN' access mode until the next scheduled change.

**href:
string
(url)




POST to this to override the zone into 'secure - no PIN' access mode until the next scheduled change.:** POST to this to override the zone into 'dual auth - no PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'dual auth - PIN' access mode until the next scheduled change.

**secureUntil:
object:** POST to this to override the zone into 'dual auth - PIN' access mode for a fixed time.

**href:
string
(url)




POST to this to override the zone into 'secure - no PIN' access mode for a fixed time.:** POST to this to forgive anti-passback for all cardholders in the zone.

**href:
string
(url):** POST to this set the zone's cardholder count.

**securePin:
object:** POST to this set the zone into lockdown mode.

**href:
string
(url)




POST to this to override the zone into 'secure - PIN' access mode until the next scheduled change.:** POST to this cancel lockdown on the zone, returning it to its scheduled mode.

**href:
string
(url):** POST to this cancel an active override on the zone, returning it to its scheduled mode. This will not affect a lockdown.

**href:
string
(url):** POST to this to override the zone into 'free - no PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'free - no PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'free - PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'free - PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'secure - no PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'secure - no PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'secure - PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'secure - PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'code or card - no PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'code or card - no PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'code or card - PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'code or card - PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'dual auth - no PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'dual auth - no PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to override the zone into 'dual auth - PIN' access mode until the next scheduled change.

**href:
string
(url):** POST to this to override the zone into 'dual auth - PIN' access mode for a fixed time.

**href:
string
(url):** POST to this to forgive anti-passback for all cardholders in the zone.

**href:
string
(url):** POST to this set the zone's cardholder count.

**href:
string
(url):** POST to this set the zone into lockdown mode.

**href:
string
(url):** POST to this cancel lockdown on the zone, returning it to its scheduled mode.

**href:
string
(url):** POST to this cancel an active override on the zone, returning it to its scheduled mode. This will not affect a lockdown.

Put this in the body of access zone override POSTs to set the count of cardholders in the access zone.

**zoneCount:
integer:** Put this in the body of access zone override POSTs to set the count of cardholders in the access zone.

An array of alarm zone summaries, and a next link for more.

An array of Alarm Zone summaries.

The link to the next page. Absent if you have retrieved them all.

**results:


Alarm Zone summary:** An array of Alarm Zone summaries.

**Alarm Zone summary:** The link to the next page. Absent if you have retrieved them all.

/api/alarm_zones returns an array of these. It is a subset of what you get from a alarm zone's detail page at /api/alarm_zones/{id} (linked as the href in this object).

A link to an
                        alarm zone detail object for this alarm zone. This is Command Centre's identifier for this alarm zone: use it whenever you need to specify an alarm zone in REST operations.

An alphanumeric identifier, unique to the server.

This is the ID to use in the source parameter of
                        event filters if you want to limit your events to particular alarm zones.

**href:
string
(url):** A link to an
                        alarm zone detail object for this alarm zone. This is Command Centre's identifier for this alarm zone: use it whenever you need to specify an alarm zone in REST operations.

**id:
string:** An alphanumeric identifier, unique to the server.
This is the ID to use in the source parameter of
                        event filters if you want to limit your events to particular alarm zones.

/api/alarm_zones/{id} returns one of these.

The division containing this Alarm Zone.

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.

Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

This block describes this item's hardware controller.

Retrieving it takes a little more time than the other fields so only ask for it if you need it.

Added in 8.50.

This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the alarm zone, changing its state.

See the section 'Understanding Alarm Zones' in the Configuration client help for a description of alarm zone states.

It will be missing if your operator does not have a privilege that allows overriding the alarm zone (examples of which are in the documentation for the POSTs).

POST to this to arm this alarm zone until the next scheduled change.

Because a site can configure different names for different alarm zone states, they appear here.

POST to this to arm this alarm zone for a fixed time.

Because a site can configure different names for different alarm zone states, they appear here.

POST to this to disarm this alarm zone until the next scheduled change.

Because a site can configure different names for different alarm zone states, they appear here.

POST to this to disarm this alarm zone for a fixed time.

Because a site can configure different names for different alarm zone states, they appear here.

POST to this to set the alarm zone's state to user1 until the next scheduled change.

Because a site can configure different names for different alarm zone states, they appear here.

POST to this to set the alarm zone's state to user1 for a fixed time.

Because a site can configure different names for different alarm zone states, they appear here.

POST to this to set the alarm zone's state to user2 until the next scheduled change.

Because a site can configure different names for different alarm zone states, they appear here.

POST to this to set the alarm zone's state to user2 for a fixed time.

Because a site can configure different names for different alarm zone states, they appear here.

POST to this cancel an active override on the alarm zone, returning it to its scheduled mode.

This command will not be available if the alarm zone is not controlled by a schedule (because without a schedule the alarm zone does not have the concept of a 'normal' state).

**description:
string:** The division containing this Alarm Zone.

**division:
object:** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**shortName:
string

 (up to 16 chars):** Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.
Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

**notes:
string:** The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

**updates:
object:** This block describes this item's hardware controller.
Retrieving it takes a little more time than the other fields so only ask for it if you need it.
Added in 8.50.

**href:
string
(url):** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**statusFlags:
string[]:** A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the alarm zone, changing its state.
See the section 'Understanding Alarm Zones' in the Configuration client help for a description of alarm zone states.
It will be missing if your operator does not have a privilege that allows overriding the alarm zone (examples of which are in the documentation for the POSTs).

**string:** POST to this to arm this alarm zone until the next scheduled change.

**connectedController:
object:** Because a site can configure different names for different alarm zone states, they appear here.

**name:
string



href:
string
(url)



This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.


id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.:** POST to this to arm this alarm zone for a fixed time.

**name:
string:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to disarm this alarm zone until the next scheduled change.

**id:
string:** Because a site can configure different names for different alarm zone states, they appear here.

**commands:
object:** POST to this to disarm this alarm zone for a fixed time.

**arm:
object






href:
string
(url)




POST to this to arm this alarm zone until the next scheduled change.


name:
string

Armed,
                                        Set


Armed


Because a site can configure different names for different alarm zone states, they appear here.





armUntil:
object






href:
string
(url)




POST to this to arm this alarm zone for a fixed time.


name:
string

Armed,
                                        Set


Armed


Because a site can configure different names for different alarm zone states, they appear here.





disarm:
object






href:
string
(url)




POST to this to disarm this alarm zone until the next scheduled change.


name:
string

Disarmed,
                                        Unset


Disarmed


Because a site can configure different names for different alarm zone states, they appear here.





disarmUntil:
object






href:
string
(url)




POST to this to disarm this alarm zone for a fixed time.


name:
string

Disarmed,
                                        Unset


Disarmed


Because a site can configure different names for different alarm zone states, they appear here.





user1:
object






href:
string
(url)




POST to this to set the alarm zone's state to user1 until the next scheduled change.


name:
string

User1


Because a site can configure different names for different alarm zone states, they appear here.





user1Until:
object






href:
string
(url)




POST to this to set the alarm zone's state to user1 for a fixed time.


name:
string

User1


Because a site can configure different names for different alarm zone states, they appear here.





user2:
object






href:
string
(url)




POST to this to set the alarm zone's state to user2 until the next scheduled change.


name:
string

User2


Because a site can configure different names for different alarm zone states, they appear here.





user2Until:
object






href:
string
(url)




POST to this to set the alarm zone's state to user2 for a fixed time.


name:
string

User2


Because a site can configure different names for different alarm zone states, they appear here.





cancel:
object






href:
string
(url)




POST to this cancel an active override on the alarm zone, returning it to its scheduled mode.
This command will not be available if the alarm zone is not controlled by a schedule (because without a schedule the alarm zone does not have the concept of a 'normal' state).:** Because a site can configure different names for different alarm zone states, they appear here.

**arm:
object:** POST to this to set the alarm zone's state to user1 until the next scheduled change.

**href:
string
(url)




POST to this to arm this alarm zone until the next scheduled change.


name:
string

Armed,
                                        Set


Armed


Because a site can configure different names for different alarm zone states, they appear here.:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to set the alarm zone's state to user1 for a fixed time.

**name:
string

Armed,
                                        Set


Armed:** Because a site can configure different names for different alarm zone states, they appear here.

**armUntil:
object:** POST to this to set the alarm zone's state to user2 until the next scheduled change.

**href:
string
(url)




POST to this to arm this alarm zone for a fixed time.


name:
string

Armed,
                                        Set


Armed


Because a site can configure different names for different alarm zone states, they appear here.:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to set the alarm zone's state to user2 for a fixed time.

**name:
string

Armed,
                                        Set


Armed:** Because a site can configure different names for different alarm zone states, they appear here.

**disarm:
object:** POST to this cancel an active override on the alarm zone, returning it to its scheduled mode.
This command will not be available if the alarm zone is not controlled by a schedule (because without a schedule the alarm zone does not have the concept of a 'normal' state).

**name:
string:** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**arm:
object:** POST to this to arm this alarm zone until the next scheduled change.

**href:
string
(url)




POST to this to arm this alarm zone until the next scheduled change.


name:
string

Armed,
                                        Set


Armed


Because a site can configure different names for different alarm zone states, they appear here.:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to arm this alarm zone for a fixed time.

**name:
string

Armed,
                                        Set


Armed:** Because a site can configure different names for different alarm zone states, they appear here.

**armUntil:
object:** POST to this to disarm this alarm zone until the next scheduled change.

**href:
string
(url)




POST to this to arm this alarm zone for a fixed time.


name:
string

Armed,
                                        Set


Armed


Because a site can configure different names for different alarm zone states, they appear here.:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to disarm this alarm zone for a fixed time.

**name:
string

Armed,
                                        Set


Armed:** Because a site can configure different names for different alarm zone states, they appear here.

**disarm:
object:** POST to this to set the alarm zone's state to user1 until the next scheduled change.

**href:
string
(url)




POST to this to disarm this alarm zone until the next scheduled change.


name:
string

Disarmed,
                                        Unset


Disarmed


Because a site can configure different names for different alarm zone states, they appear here.:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to set the alarm zone's state to user1 for a fixed time.

**name:
string

Disarmed,
                                        Unset


Disarmed:** Because a site can configure different names for different alarm zone states, they appear here.

**disarmUntil:
object:** POST to this to set the alarm zone's state to user2 until the next scheduled change.

**href:
string
(url)




POST to this to disarm this alarm zone for a fixed time.


name:
string

Disarmed,
                                        Unset


Disarmed


Because a site can configure different names for different alarm zone states, they appear here.:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to set the alarm zone's state to user2 for a fixed time.

**name:
string

Disarmed,
                                        Unset


Disarmed:** Because a site can configure different names for different alarm zone states, they appear here.

**user1:
object:** POST to this cancel an active override on the alarm zone, returning it to its scheduled mode.
This command will not be available if the alarm zone is not controlled by a schedule (because without a schedule the alarm zone does not have the concept of a 'normal' state).

**href:
string
(url):** POST to this to arm this alarm zone until the next scheduled change.

**name:
string

Armed,
                                        Set


Armed:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to arm this alarm zone for a fixed time.

**name:
string

Armed,
                                        Set


Armed:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to disarm this alarm zone until the next scheduled change.

**name:
string

Disarmed,
                                        Unset


Disarmed:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to disarm this alarm zone for a fixed time.

**name:
string

Disarmed,
                                        Unset


Disarmed:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to set the alarm zone's state to user1 until the next scheduled change.

**name:
string

User1:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to set the alarm zone's state to user1 for a fixed time.

**name:
string

User1:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to set the alarm zone's state to user2 until the next scheduled change.

**name:
string

User2:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this to set the alarm zone's state to user2 for a fixed time.

**name:
string

User2:** Because a site can configure different names for different alarm zone states, they appear here.

**href:
string
(url):** POST to this cancel an active override on the alarm zone, returning it to its scheduled mode.
This command will not be available if the alarm zone is not controlled by a schedule (because without a schedule the alarm zone does not have the concept of a 'normal' state).

An array of day categories, and a next link for more.

An array of day categories.

The link to the next page. Absent if you have retrieved them all.

**results:


Day category:** An array of day categories.

**Day category:** The link to the next page. Absent if you have retrieved them all.

/api/day_categories returns an array of these. Each gives you enough about a day category to identify it and use it in a schedule: its href, name, and (if you ask for them using the fields parameter) notes and description.

This is the string to use when placing a day category on a schedule.

Not in the default field set. If you want it, you need to ask for it using fields.

Also not in the default field set. If you want it, you need to ask for it.

**name:
string:** This is the string to use when placing a day category on a schedule.

**href:
string
(url):** Not in the default field set. If you want it, you need to ask for it using fields.

**description:
string:** Also not in the default field set. If you want it, you need to ask for it.

An array of door summaries, and a next link for more.

An array of door summaries.

The link to the next page. Absent if you have retrieved them all.

**results:


Door summary:** An array of door summaries.

**Door summary:** The link to the next page. Absent if you have retrieved them all.

/api/doors returns an array of these. It is a subset of what you get from a door's detail page at /api/doors/{id} (linked as the href in this object).

A link to a
                        door detail object for this door.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                        event filters when you are interested in events originating at this door.

**href:
string
(url):** A link to a
                        door detail object for this door.

**id:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                        event filters when you are interested in events originating at this door.

/api/doors/{id} returns one of these.

The division containing this door.

The name and href of the access zone to which this door allows entry.

This is the address of the
                                  access zone's detail page (which includes a link back here).

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

Short names are not displayed by default. You must ask for them using the 'fields' parameter:

?fields=shortname,....

Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.

Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

An array of commands, each represented by a block containing an href that accepts a POST to send an override. The only override you can send to a door is 'open'.

If your operator is privileged to override the door's entry zone, and the zone only has one door, there will be 17 more links: four each of the four access zone modes, plus 'cancel'. Each zone mode has four variants: with or without PINs, and with or without an end time.

This block will only contain the links that your operator is privileged to perform. Examples of the privileges you need are in the documentation for the POSTs.

The zone overrides are not repeated here.

POST to this to open this door.

This block describes this door's hardware controller.

Retrieving it takes a little more time than the other fields so only ask for it if you need it, if your doors are legion.

This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because in 8.30 there is no interface for hardware controllers. GETting the URL will return a 404.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**description:
string:** The division containing this door.

**division:
object:** The name and href of the access zone to which this door allows entry.

**entryAccessZone:
object:** This is the address of the
                                  access zone's detail page (which includes a link back here).

**name:
string



href:
string
(url)



This is the address of the
                                  access zone's detail page (which includes a link back here).:** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**name:
string:** Short names are not displayed by default. You must ask for them using the 'fields' parameter:
?fields=shortname,....

**href:
string
(url):** Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.
Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

**notes:
string:** The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

**shortName:
string

 (up to 16 chars):** An array of commands, each represented by a block containing an href that accepts a POST to send an override. The only override you can send to a door is 'open'.
If your operator is privileged to override the door's entry zone, and the zone only has one door, there will be 17 more links: four each of the four access zone modes, plus 'cancel'. Each zone mode has four variants: with or without PINs, and with or without an end time.
This block will only contain the links that your operator is privileged to perform. Examples of the privileges you need are in the documentation for the POSTs.
The zone overrides are not repeated here.

**updates:
object:** POST to this to open this door.

**href:
string
(url):** This block describes this door's hardware controller.
Retrieving it takes a little more time than the other fields so only ask for it if you need it, if your doors are legion.

**href:
string
(url):** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because in 8.30 there is no interface for hardware controllers. GETting the URL will return a 404.

**statusFlags:
string[]:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**name:
string:** This is the address of the
                                  access zone's detail page (which includes a link back here).

**open:
object:** POST to this to open this door.

**href:
string
(url):** POST to this to open this door.

**name:
string:** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because in 8.30 there is no interface for hardware controllers. GETting the URL will return a 404.

**href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

The
                    elevator group search returns an array of these, if you don't use the fields parameter to ask for more. It is a subset of what you get from the 'modify passenger details' search or the elevator group's detail page (which is linked as the href in this object).

A link to a
                        elevator group object for this elevator group.

**href:
string
(url):** A link to a
                        elevator group object for this elevator group.

The
                    modify passenger details search returns an array of these. It is everything you need to pick a default floor for a cardholder, but a subset of what you get from a elevator group's detail page (which is linked as the href in this object).

The division containing this elevator group.

An array of objects describing the floors in this elevator group.

This example only has one floor. Expect more from production systems.

This is Command Centre's internal identifier for the floor. Passengers do not see it.

True iff the elevator car has a front-facing door and the elevator services the floor with the front of the elevator.

This field requires the RESTStatus licence in versions up to and including 8.50. Starting with 8.60 it requires either the RESTStatus or the RESTOverrides licence.

True iff the elevator car has a rear-facing door and the elevator services the floor with the rear of the elevator.

This field requires the RESTStatus licence in versions up to and including 8.50. Starting with 8.60 it requires either the RESTStatus or the RESTOverrides licence.

This is a friendly name that Command Centre presents to operators as the name of the floor.

The access zone into which the elevator car's front door opens. It will be missing if the car's front door does not open on ths floor.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.

The access zone into which the elevator car's rear door opens. It will be missing if the car's rear door does not open on this floor, or if the elevator group's rearAccessEnabled is false.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.

**division:
object:** The division containing this elevator group.

**floorAccess:
object[]:** An array of objects describing the floors in this elevator group.
This example only has one floor. Expect more from production systems.

**object





floorNumber:
integer
x ≥ 1


This is Command Centre's internal identifier for the floor. Passengers do not see it.


frontService:
boolean



True iff the elevator car has a front-facing door and the elevator services the floor with the front of the elevator.
This field requires the RESTStatus licence in versions up to and including 8.50. Starting with 8.60 it requires either the RESTStatus or the RESTOverrides licence.


rearService:
boolean



True iff the elevator car has a rear-facing door and the elevator services the floor with the rear of the elevator.
This field requires the RESTStatus licence in versions up to and including 8.50. Starting with 8.60 it requires either the RESTStatus or the RESTOverrides licence.


floorName:
string



This is a friendly name that Command Centre presents to operators as the name of the floor.


frontAccessZone:
object



The access zone into which the elevator car's front door opens. It will be missing if the car's front door does not open on ths floor.





id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.


name:
string



href:
string
(url)






rearAccessZone:
object



The access zone into which the elevator car's rear door opens. It will be missing if the car's rear door does not open on this floor, or if the elevator group's rearAccessEnabled is false.





id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.


name:
string



href:
string
(url):** This is Command Centre's internal identifier for the floor. Passengers do not see it.

**floorNumber:
integer
x ≥ 1:** True iff the elevator car has a front-facing door and the elevator services the floor with the front of the elevator.
This field requires the RESTStatus licence in versions up to and including 8.50. Starting with 8.60 it requires either the RESTStatus or the RESTOverrides licence.

**frontService:
boolean:** True iff the elevator car has a rear-facing door and the elevator services the floor with the rear of the elevator.
This field requires the RESTStatus licence in versions up to and including 8.50. Starting with 8.60 it requires either the RESTStatus or the RESTOverrides licence.

**rearService:
boolean:** This is a friendly name that Command Centre presents to operators as the name of the floor.

**floorName:
string:** The access zone into which the elevator car's front door opens. It will be missing if the car's front door does not open on ths floor.

**frontAccessZone:
object:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.

**id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.


name:
string



href:
string
(url):** The access zone into which the elevator car's rear door opens. It will be missing if the car's rear door does not open on this floor, or if the elevator group's rearAccessEnabled is false.

**id:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.

**floorNumber:
integer
x ≥ 1:** This is Command Centre's internal identifier for the floor. Passengers do not see it.

**frontService:
boolean:** True iff the elevator car has a front-facing door and the elevator services the floor with the front of the elevator.
This field requires the RESTStatus licence in versions up to and including 8.50. Starting with 8.60 it requires either the RESTStatus or the RESTOverrides licence.

**rearService:
boolean:** True iff the elevator car has a rear-facing door and the elevator services the floor with the rear of the elevator.
This field requires the RESTStatus licence in versions up to and including 8.50. Starting with 8.60 it requires either the RESTStatus or the RESTOverrides licence.

**floorName:
string:** This is a friendly name that Command Centre presents to operators as the name of the floor.

**frontAccessZone:
object:** The access zone into which the elevator car's front door opens. It will be missing if the car's front door does not open on ths floor.

**id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.


name:
string



href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.

**id:
string:** The access zone into which the elevator car's rear door opens. It will be missing if the car's rear door does not open on this floor, or if the elevator group's rearAccessEnabled is false.

**name:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.

**id:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.

**id:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                            event filters and in the body of
                                            status subscriptions.

/api/elevator_groups/{id} returns one of these.

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

Short names are not displayed by default. You must ask for them using the 'fields' parameter:

?fields=shortname,....

The elevator system's internal identifier for this elevator group.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

True only if the elevator group uses rear doors on its cars to service the other side of the shaft.

The identifier of the floor that this elevator group calls 'ground'.

**description:
string:** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**notes:
string:** Short names are not displayed by default. You must ask for them using the 'fields' parameter:
?fields=shortname,....

**shortName:
string

 (up to 16 chars):** The elevator system's internal identifier for this elevator group.

**elevatorGroupNumber:
integer:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**elevatorSystem:
object:** True only if the elevator group uses rear doors on its cars to service the other side of the shaft.

**id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.:** The identifier of the floor that this elevator group calls 'ground'.

**id:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

An array of fence zone summaries, and a next link for more.

An array of Fence Zone summaries.

The link to the next page. Absent if you have retrieved them all.

**results:


Fence Zone summary:** An array of Fence Zone summaries.

**Fence Zone summary:** The link to the next page. Absent if you have retrieved them all.

/api/fence_zones returns an array of these. It is a subset of what you get from a fence zone's detail page at /api/fence_zones/{id} (linked as the href in this object).

A link to a
                        fence zone detail object for this fence zone. This is Command Centre's identifier for this fence zone: use it whenever you need to specify a fence zone in REST operations.

An alphanumeric identifier, unique to the server.

This is the ID to use in the source parameter of
                        event filters if you want to limit your events to those coming from particular fence zones.

**href:
string
(url):** A link to a
                        fence zone detail object for this fence zone. This is Command Centre's identifier for this fence zone: use it whenever you need to specify a fence zone in REST operations.

**id:
string:** An alphanumeric identifier, unique to the server.
This is the ID to use in the source parameter of
                        event filters if you want to limit your events to those coming from particular fence zones.

/api/fence_zones/{id} returns one of these.

The division containing this Fence Zone.

The last known voltage on the fence, in volts, to the nearest 100 volts, provided the fence zone is online and the REST server is subscribed to its updates. See the
                            item status section for how to make sure of that.

This value is only up to date when the fence zone is on (shown in the status flags) and has emitted at least one pulse.

Voltages are not displayed by default, because of the caveats around their use. You must ask for them using the 'fields' parameter: ?fields=voltage,....

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

Short names are not displayed by default. You must ask for them using the 'fields' parameter:

?fields=shortname,....

Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.

Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

This block describes this item's hardware controller.

Retrieving it takes a little more time than the other fields so only ask for it if you need it.

Added in 8.50.

This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the fence zone, changing its state.

It will be missing if your operator is not privileged to override the fence zone.

See the section 'Creating a Fence Zone' in the Configuration client help for a description of fence zone states.

POST to this to energise this fence zone.

POST to this to deactivate this fence zone.

POST to this to shunt this fence zone.

POST to this to restore communication with fence zone.

POST to this to set this fence zone to 'high voltage' mode.

POST to this to set this fence zone to 'low feel' mode.

POST to this cancel an active override on the fence zone.

**description:
string:** The division containing this Fence Zone.

**division:
object:** The last known voltage on the fence, in volts, to the nearest 100 volts, provided the fence zone is online and the REST server is subscribed to its updates. See the
                            item status section for how to make sure of that.
This value is only up to date when the fence zone is on (shown in the status flags) and has emitted at least one pulse.
Voltages are not displayed by default, because of the caveats around their use. You must ask for them using the 'fields' parameter: ?fields=voltage,....

**voltage:
integer
x ≥ 0:** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**notes:
string:** Short names are not displayed by default. You must ask for them using the 'fields' parameter:
?fields=shortname,....

**shortName:
string

 (up to 16 chars):** Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.
Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

**updates:
object:** The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

**href:
string
(url):** This block describes this item's hardware controller.
Retrieving it takes a little more time than the other fields so only ask for it if you need it.
Added in 8.50.

**href:
string
(url):** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**statusFlags:
string[]:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**string:** A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the fence zone, changing its state.
It will be missing if your operator is not privileged to override the fence zone.
See the section 'Creating a Fence Zone' in the Configuration client help for a description of fence zone states.

**connectedController:
object:** POST to this to energise this fence zone.

**name:
string



href:
string
(url)



This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.


id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.:** POST to this to deactivate this fence zone.

**name:
string:** POST to this to shunt this fence zone.

**href:
string
(url):** POST to this to restore communication with fence zone.

**id:
string:** POST to this to set this fence zone to 'high voltage' mode.

**commands:
object:** POST to this to set this fence zone to 'low feel' mode.

**on:
object






href:
string
(url)




POST to this to energise this fence zone.





off:
object






href:
string
(url)




POST to this to deactivate this fence zone.





shunt:
object






href:
string
(url)




POST to this to shunt this fence zone.





unshunt:
object






href:
string
(url)




POST to this to restore communication with fence zone.





highVoltage:
object






href:
string
(url)




POST to this to set this fence zone to 'high voltage' mode.





lowFeel:
object






href:
string
(url)




POST to this to set this fence zone to 'low feel' mode.





cancel:
object






href:
string
(url)




POST to this cancel an active override on the fence zone.:** POST to this cancel an active override on the fence zone.

**name:
string:** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**on:
object:** POST to this to energise this fence zone.

**href:
string
(url)




POST to this to energise this fence zone.:** POST to this to deactivate this fence zone.

**href:
string
(url):** POST to this to shunt this fence zone.

**off:
object:** POST to this to restore communication with fence zone.

**href:
string
(url)




POST to this to deactivate this fence zone.:** POST to this to set this fence zone to 'high voltage' mode.

**href:
string
(url):** POST to this to set this fence zone to 'low feel' mode.

**shunt:
object:** POST to this cancel an active override on the fence zone.

**href:
string
(url):** POST to this to energise this fence zone.

**href:
string
(url):** POST to this to deactivate this fence zone.

**href:
string
(url):** POST to this to shunt this fence zone.

**href:
string
(url):** POST to this to restore communication with fence zone.

**href:
string
(url):** POST to this to set this fence zone to 'high voltage' mode.

**href:
string
(url):** POST to this to set this fence zone to 'low feel' mode.

**href:
string
(url):** POST to this cancel an active override on the fence zone.

An array of input summaries, and a next link for more.

An array of input summaries.

The link to the next page. Absent if you have retrieved them all.

**results:


Input summary:** An array of input summaries.

**Input summary:** The link to the next page. Absent if you have retrieved them all.

/api/inputs returns an array of these. It is a subset of what you get from a input's detail page at /api/inputs/{id} (linked as the href in this object).

A link to an
                        input detail object for this input.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                        event filters if you are only interested in events from particular inputs.

**href:
string
(url):** A link to an
                        input detail object for this input.

**id:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                        event filters if you are only interested in events from particular inputs.

/api/inputs/{id} returns one of these.

The division containing this input.

Short names are not displayed by default. You must ask for them using the 'fields' parameter:

?fields=shortname,....

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.

Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

This block describes this item's hardware controller.

Retrieving it takes a little more time than the other fields so only ask for it if you need it.

Added in 8.50.

This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the input.

It will be missing if your operator does not have a privilege that allows overriding the input (examples of which are in the documentation for the POSTs).

POST to this to shunt the input.

POST to this to stop shunting the input.

POST to this to isolate the input. Isolated inputs do not prevent alarm zones from arming.

The link will be missing if the input is shunted.

POST to this to stop isolating the input. The link will be missing if the input is shunted.

**description:
string:** The division containing this input.

**division:
object:** Short names are not displayed by default. You must ask for them using the 'fields' parameter:
?fields=shortname,....

**shortName:
string

 (up to 16 chars):** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**notes:
string:** Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.
Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

**updates:
object:** The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

**href:
string
(url):** This block describes this item's hardware controller.
Retrieving it takes a little more time than the other fields so only ask for it if you need it.
Added in 8.50.

**href:
string
(url):** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**statusFlags:
string[]:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**string:** A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the input.
It will be missing if your operator does not have a privilege that allows overriding the input (examples of which are in the documentation for the POSTs).

**connectedController:
object:** POST to this to shunt the input.

**name:
string



href:
string
(url)



This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.


id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.:** POST to this to stop shunting the input.

**name:
string:** POST to this to isolate the input. Isolated inputs do not prevent alarm zones from arming.
The link will be missing if the input is shunted.

**href:
string
(url):** POST to this to stop isolating the input. The link will be missing if the input is shunted.

**name:
string:** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**shunt:
object:** POST to this to shunt the input.

**href:
string
(url)




POST to this to shunt the input.:** POST to this to stop shunting the input.

**href:
string
(url):** POST to this to isolate the input. Isolated inputs do not prevent alarm zones from arming.
The link will be missing if the input is shunted.

**unshunt:
object:** POST to this to stop isolating the input. The link will be missing if the input is shunted.

**href:
string
(url):** POST to this to shunt the input.

**href:
string
(url):** POST to this to stop shunting the input.

**href:
string
(url):** POST to this to isolate the input. Isolated inputs do not prevent alarm zones from arming.
The link will be missing if the input is shunted.

**href:
string
(url):** POST to this to stop isolating the input. The link will be missing if the input is shunted.

API support for interlocks is still in development and may change in future versions.

An array of interlock group summaries and a next link for more.

An array of interlock group summaries.

The link to the next page. Absent if you have retrieved them all.

**results:


Interlock Group summary:** An array of interlock group summaries.

**Interlock Group summary:** The link to the next page. Absent if you have retrieved them all.

API support for interlocks is still in development and may change in future versions.

/api/interlock_groups returns an array of these. It is a subset of what you get from an interlock's details page at /api/interlock_groups/{id} (linked as the href in this object).

A link to an
                        interlock group detail object for this item.

**href:
string
(url):** A link to an
                        interlock group detail object for this item.

API support for interlocks is still in development and may change in future versions.

/api/interlock_groups/{id} returns one of these.

Some of the fields will not come from the server unless you request them via the fields query parameter.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                            event filters and in the body of
                            status subscriptions.

The division containing this interlock group.

Short names are not displayed by default. You must ask for them using the 'fields' parameter:

?fields=shortname,....

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.

Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

This field contains a translated multi-line human-readable description of the item's status. See the statusFlags field for notes on when you should ask for this field and how to keep it up to date.

This field contains the statusText field with line endings turned into spaces to make a one-line string. See the statusFlags field for notes on when you should ask for this field and how to keep it up to date.

This block describes this item's hardware controller.

Retrieving it takes a little more time than the other fields so only ask for it if you need it.

Added in 8.50.

This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the interlock.

It will be missing if your operator does not have a privilege that allows overriding the interlock (examples of which are in the documentation for the POSTs).

POST to this to disable the interlock group, allowing the doors to operate independently.

POST to re-enable the interlock group, causing its doors to return to interlock behaviour.

**id:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                            event filters and in the body of
                            status subscriptions.

**description:
string:** The division containing this interlock group.

**division:
object:** Short names are not displayed by default. You must ask for them using the 'fields' parameter:
?fields=shortname,....

**shortName:
string

 (up to 16 chars):** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**notes:
string:** Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.
Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

**updates:
object:** The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

**href:
string
(url):** This field contains a translated multi-line human-readable description of the item's status. See the statusFlags field for notes on when you should ask for this field and how to keep it up to date.

**href:
string
(url):** This field contains the statusText field with line endings turned into spaces to make a one-line string. See the statusFlags field for notes on when you should ask for this field and how to keep it up to date.

**statusFlags:
string[]:** This block describes this item's hardware controller.
Retrieving it takes a little more time than the other fields so only ask for it if you need it.
Added in 8.50.

**string:** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**statusText:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**status:
string:** A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the interlock.
It will be missing if your operator does not have a privilege that allows overriding the interlock (examples of which are in the documentation for the POSTs).

**connectedController:
object:** POST to this to disable the interlock group, allowing the doors to operate independently.

**name:
string



href:
string
(url)



This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.


id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.:** POST to re-enable the interlock group, causing its doors to return to interlock behaviour.

**name:
string:** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**disable:
object:** POST to this to disable the interlock group, allowing the doors to operate independently.

**href:
string
(url)




POST to this to disable the interlock group, allowing the doors to operate independently.:** POST to re-enable the interlock group, causing its doors to return to interlock behaviour.

**href:
string
(url):** POST to this to disable the interlock group, allowing the doors to operate independently.

**href:
string
(url):** POST to re-enable the interlock group, causing its doors to return to interlock behaviour.

An array of macro summaries, and a next link for more.

An array of macro summaries.

The link to the next page. Absent if you have retrieved them all.

**results:


Macro summary:** An array of macro summaries.

**Macro summary:** The link to the next page. Absent if you have retrieved them all.

/api/macros returns an array of these. It is a subset of what you get from a macro's detail page at /api/macros/{id} (linked as the href in this object).

A link to a
                        macro detail object for this macro.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                        event filters if you are only interested in events from particular macros (which would be unusual, since macros do not generate many events).

**href:
string
(url):** A link to a
                        macro detail object for this macro.

**id:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                        event filters if you are only interested in events from particular macros (which would be unusual, since macros do not generate many events).

/api/macros/{id} returns one of these.

It contains a block called updates. This is reserved for future development and its behaviour will change in later versions of Command Centre.

The division containing this Macro.

Short names are not displayed by default. You must ask for them using the 'fields' parameter:

?fields=shortname,....

The only thing you can do to a macro via REST is run it, so this block only contains one command, and only if your operator is privileged to do that.

POST to this to run this macro.

**description:
string:** The division containing this Macro.

**division:
object:** Short names are not displayed by default. You must ask for them using the 'fields' parameter:
?fields=shortname,....

**shortName:
string

 (up to 16 chars):** The only thing you can do to a macro via REST is run it, so this block only contains one command, and only if your operator is privileged to do that.

**commands:
object:** POST to this to run this macro.

**run:
object:** POST to this to run this macro.

**href:
string
(url):** POST to this to run this macro.

An array of output summaries, and a next link for more.

An array of output summaries.

The link to the next page. Absent if you have retrieved them all.

**results:


Output summary:** An array of output summaries.

**Output summary:** The link to the next page. Absent if you have retrieved them all.

/api/outputs returns an array of these. It is a subset of what you get from a output's detail page at /api/outputs/{id} (linked as the href in this object).

A link to an
                        output detail object for this output.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                        event filters if you are only interested in events from particular outputs.

**href:
string
(url):** A link to an
                        output detail object for this output.

**id:
string:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                        event filters if you are only interested in events from particular outputs.

/api/outputs/{id} returns one of these.

The division containing this output.

Short names are not displayed by default. You must ask for them using the 'fields' parameter:

?fields=shortname,....

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.

Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

This block describes this item's hardware controller.

Retrieving it takes a little more time than the other fields so only ask for it if you need it.

Added in 8.50.

This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the output, turning it on or off.

It will be missing if your operator does not have a privilege that allows overriding the output (examples of which are in the documentation for the POSTs).

POST to this to close this output.

POST to this to close this output for a fixed time.

POST to this to open this output.

POST to this to open this output for a fixed time.

POST to this to close this output for its pulse time.

This link will only be here if the output is set to pulse when activated.

Added in 8.50.

**description:
string:** The division containing this output.

**division:
object:** Short names are not displayed by default. You must ask for them using the 'fields' parameter:
?fields=shortname,....

**shortName:
string

 (up to 16 chars):** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**notes:
string:** Follow the URL in the href inside this block to receive the item's current status, then follow the next link in the results to long poll for changes to that status.
Update pages take the same fields parameter as summary and details pages. You should use that to request all the fields you need in the update.

**updates:
object:** The search and details pages do not return status flags by default, because an item's status is unknown until something is monitoring it. If you want status flags on the search and details pages you must ask for them using the fields parameter, but our advice is to monitor them using
                            status subscriptions if you are running 8.30 or later, otherwise the item's updates link. See the
                            item status section for a full description of how to stay up to date with item status, and this item's introduction in the Operations section for what flags this item might return and what they mean.

**href:
string
(url):** This block describes this item's hardware controller.
Retrieving it takes a little more time than the other fields so only ask for it if you need it.
Added in 8.50.

**href:
string
(url):** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**statusFlags:
string[]:** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**string:** A block of commands, each represented by a block containing an href that accepts a POST that will send an override to the output, turning it on or off.
It will be missing if your operator does not have a privilege that allows overriding the output (examples of which are in the documentation for the POSTs).

**connectedController:
object:** POST to this to close this output.

**name:
string



href:
string
(url)



This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.


id:
string



An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.:** POST to this to close this output for a fixed time.

**name:
string:** POST to this to open this output.

**href:
string
(url):** POST to this to open this output for a fixed time.

**id:
string:** POST to this to close this output for its pulse time.
This link will only be here if the output is set to pulse when activated.
Added in 8.50.

**name:
string:** This is the REST API's identifier for the hardware controller. It is only an identifier, not a usable URL, because there is no interface for hardware controllers. GETting the URL will return a 404.

**href:
string
(url):** An alphanumeric identifier, unique to the server. This is the ID to use in the source parameter of
                                  event filters and in the body of
                                  status subscriptions.

**on:
object:** POST to this to close this output.

**href:
string
(url)




POST to this to close this output.:** POST to this to close this output for a fixed time.

**href:
string
(url):** POST to this to open this output.

**onUntil:
object:** POST to this to open this output for a fixed time.

**href:
string
(url)




POST to this to close this output for a fixed time.:** POST to this to close this output for its pulse time.
This link will only be here if the output is set to pulse when activated.
Added in 8.50.

**href:
string
(url):** POST to this to close this output.

**href:
string
(url):** POST to this to close this output for a fixed time.

**href:
string
(url):** POST to this to open this output.

**href:
string
(url):** POST to this to open this output for a fixed time.

**href:
string
(url):** POST to this to close this output for its pulse time.
This link will only be here if the output is set to pulse when activated.
Added in 8.50.

An array of schedule summaries, and a next link for more.

An array of schedule summaries.

The link to the next page. Absent if you have retrieved them all.

**results:


Schedule summary:** An array of schedule summaries.

**Schedule summary:** The link to the next page. Absent if you have retrieved them all.

A
                    schedule search returns an array of these. It is a subset of what you get from a detail page at /api/schedules/{id} (linked as the href in this object).

A link to a
                        schedule detail.

**href:
string
(url):** A link to a
                        schedule detail.

/api/schedules/{id} returns one of these, and you put one of these in the body of a POST or PATCH to create or edit a schedule.

The division containing this schedule. It is possible to change a schedule's division after creating it.

Because of their potential size, notes are only available by request. Use the 'fields' parameter:

?fields=defaults,notes,...

This block contains a string field type which will (in the server's response to a GET) or must (in the body of your POST) be one of the seven schedule types. It is not a valid field in a PATCH, because you cannot change the type of an existing schedule.

Elevator kiosk control schedules arrived in 8.60.

This is the part of the schedule that controls when changes occur on its scheduled items.

It is an array of objects, each containing a day category and an array of times. The day category picks days of the year, and the times array sets what will happen and at what times on those days.

In the body of your POST:

The time field of each object inside times must be a string of the form HH:MM between 00:00 and 23:59. If you find another format that works (four zeroes, for example), it may not work in future versions.

Each day category must have an entry at 00:00. This means an item does not need to search back in time to find what state it should be in when it first comes online.

Each day category cannot have more than one entry at the same time.

If you receive a 400 response to your POST or PATCH and one of those rules is the cause, the body of the response should contain a reminder.

The state field is an array describing what should happen at that time. It is comparable to the statusFlags arrays on access zones, alarm zones, outputs, and fence zones. For most schedule types it will contain only one word, but the extra flag usePin may accompany access zone state changes.

When building your JSON, treat these string comparisons as case-sensitive.

All schedule types can have a state change called 'cancelUntimedOverrides'. That sets an item back to its scheduled state if it was under the effect of an override with no end time.

Other than that, each schedule type has its own set of state changes:

An Access Schedule, also known as a Cardholder Access Schedule, controls the ability of the members of an access group to pass into an access zone. The valid states are grant and deny. Note that 'deny' is a misnomer: a cardholder will gain access through a door if they are a member of a different access group that still has access.

An Access Zone Schedule controls the mode of an access zone. The valid states are the same as the
                                zone's status flags: secure, dualAuth, codeOrCard, or free. The extra flag usePin means the same here as it does in the status flags: people will need their PINs at readers and alarms terminals.

An Alarm Zone Schedule switches an alarm zone between its four modes: set, unset, user, and user2. Use the words user1 and user2 here even if you have renamed them in the server properties.

An Output Schedule can be on or off, plain and simple.

A Notification Schedule controls when notifications go out. They can make sure that notifications go to the people who are on shift, and they can prevent bothering people at night. Like the access and output schedules it is binary, but the valid states are called notificationEnabled and notificationDisabled.

A HV/LF Schedule controls the voltage on an electric fence. 'lowFeel' mode allows detection without the deterrant of highVoltate.

This example sets an access zone to secure mode between 7.30am and 6:00pm on work days, and secure plus PIN mode at all other times.

An array containing the names and hrefs of all the items that this schedule controls and that the operator has the privilege to view.

This is generated data. A schedule resides in the configuration of the items that use it, rather than the other way around, so this block is a handy aggregation of the inverse of those relationships. As such it is read-only.

- The time field of each object inside times must be a string of the form HH:MM between 00:00 and 23:59. If you find another format that works (four zeroes, for example), it may not work in future versions.

- Each day category must have an entry at 00:00. This means an item does not need to search back in time to find what state it should be in when it first comes online.

- Each day category cannot have more than one entry at the same time.

- An Access Schedule, also known as a Cardholder Access Schedule, controls the ability of the members of an access group to pass into an access zone. The valid states are grant and deny. Note that 'deny' is a misnomer: a cardholder will gain access through a door if they are a member of a different access group that still has access.

- An Access Zone Schedule controls the mode of an access zone. The valid states are the same as the
                                zone's status flags: secure, dualAuth, codeOrCard, or free. The extra flag usePin means the same here as it does in the status flags: people will need their PINs at readers and alarms terminals.

- An Alarm Zone Schedule switches an alarm zone between its four modes: set, unset, user, and user2. Use the words user1 and user2 here even if you have renamed them in the server properties.

- An Output Schedule can be on or off, plain and simple.

- A Notification Schedule controls when notifications go out. They can make sure that notifications go to the people who are on shift, and they can prevent bothering people at night. Like the access and output schedules it is binary, but the valid states are called notificationEnabled and notificationDisabled.

- A HV/LF Schedule controls the voltage on an electric fence. 'lowFeel' mode allows detection without the deterrant of highVoltate.

**description:
string:** The division containing this schedule. It is possible to change a schedule's division after creating it.

**division:
object:** Because of their potential size, notes are only available by request. Use the 'fields' parameter:
?fields=defaults,notes,...

**notes:
string:** This block contains a string field type which will (in the server's response to a GET) or must (in the body of your POST) be one of the seven schedule types. It is not a valid field in a PATCH, because you cannot change the type of an existing schedule.
Elevator kiosk control schedules arrived in 8.60.

**type:
object:** This is the part of the schedule that controls when changes occur on its scheduled items.
It is an array of objects, each containing a day category and an array of times. The day category picks days of the year, and the times array sets what will happen and at what times on those days.
In the body of your POST:


The time field of each object inside times must be a string of the form HH:MM between 00:00 and 23:59. If you find another format that works (four zeroes, for example), it may not work in future versions.


Each day category must have an entry at 00:00. This means an item does not need to search back in time to find what state it should be in when it first comes online.


Each day category cannot have more than one entry at the same time.


If you receive a 400 response to your POST or PATCH and one of those rules is the cause, the body of the response should contain a reminder.
The state field is an array describing what should happen at that time. It is comparable to the statusFlags arrays on access zones, alarm zones, outputs, and fence zones. For most schedule types it will contain only one word, but the extra flag usePin may accompany access zone state changes.
When building your JSON, treat these string comparisons as case-sensitive.
All schedule types can have a state change called 'cancelUntimedOverrides'. That sets an item back to its scheduled state if it was under the effect of an override with no end time.
Other than that, each schedule type has its own set of state changes:


An Access Schedule, also known as a Cardholder Access Schedule, controls the ability of the members of an access group to pass into an access zone. The valid states are grant and deny. Note that 'deny' is a misnomer: a cardholder will gain access through a door if they are a member of a different access group that still has access.


An Access Zone Schedule controls the mode of an access zone. The valid states are the same as the
                                zone's status flags: secure, dualAuth, codeOrCard, or free. The extra flag usePin means the same here as it does in the status flags: people will need their PINs at readers and alarms terminals.


An Alarm Zone Schedule switches an alarm zone between its four modes: set, unset, user, and user2. Use the words user1 and user2 here even if you have renamed them in the server properties.


An Output Schedule can be on or off, plain and simple.


A Notification Schedule controls when notifications go out. They can make sure that notifications go to the people who are on shift, and they can prevent bothering people at night. Like the access and output schedules it is binary, but the valid states are called notificationEnabled and notificationDisabled.


A HV/LF Schedule controls the voltage on an electric fence. 'lowFeel' mode allows detection without the deterrant of highVoltate.


This example sets an access zone to secure mode between 7.30am and 6:00pm on work days, and secure plus PIN mode at all other times.

**type:
string

accessZoneSchedule,
                                  accessSchedule,
                                  alarmZoneSchedule,
                                  outputSchedule,
                                  notificationSchedule,
                                  hVLFSchedule,
                                  elevatorKioskControlSchedule:** An array containing the names and hrefs of all the items that this schedule controls and that the operator has the privilege to view.
This is generated data. A schedule resides in the configuration of the items that use it, rather than the other way around, so this block is a handy aggregation of the inverse of those relationships. As such it is read-only.

/api/schedules expects one of these in the body of a POST or a PATCH.

The PATCH will ignore the type field, because a schedule cannot change its type.

The division into which you want to place this schedule.

This block contains a string field type which must be one of the seven schedule types. It is not a valid field in a PATCH, because you cannot change the type of an existing schedule.

Elevator kiosk control schedules arrived in version 8.60.

See the description in the
                        details page.

This example sets an access zone to secure mode between 7.30am and 6:00pm on work days, and secure plus PIN mode at all other times.

**name:
string:** The division into which you want to place this schedule.

**description:
string:** This block contains a string field type which must be one of the seven schedule types. It is not a valid field in a PATCH, because you cannot change the type of an existing schedule.
Elevator kiosk control schedules arrived in version 8.60.

**division:
object:** See the description in the
                        details page.
This example sets an access zone to secure mode between 7.30am and 6:00pm on work days, and secure plus PIN mode at all other times.

Put this in the body of override POSTs to set the time at which the override should cease. The API will reject the override if the string is not empty and it cannot parse it into a date-time, but it will treat the override as having no end time if you mis-spell 'endTime' or if you send a blank string.

Command Centre computes an override's duration to a whole number of minutes with a minimum of one. That means that a timed override will always end at a multiple of sixty seconds from the time the hardware receives the override request, which will be within thirty seconds of the time you supplied here. In versions older than 8.80, the discrepancy may be up to a minute.

Careful observation of overrides submitted from the Configuration client and from the API will reveal that they use different rounding methods. Be assured, both result in overrides ending within a minute of the requested time.

**endTime:
string
(date-time):** Put this in the body of override POSTs to set the time at which the override should cease. The API will reject the override if the string is not empty and it cannot parse it into a date-time, but it will treat the override as having no end time if you mis-spell 'endTime' or if you send a blank string.
Command Centre computes an override's duration to a whole number of minutes with a minimum of one. That means that a timed override will always end at a multiple of sixty seconds from the time the hardware receives the override request, which will be within thirty seconds of the time you supplied here. In versions older than 8.80, the discrepancy may be up to a minute.
Careful observation of overrides submitted from the Configuration client and from the API will reveal that they use different rounding methods. Be assured, both result in overrides ending within a minute of the requested time.
