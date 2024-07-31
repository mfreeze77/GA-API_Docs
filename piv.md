# Command Centre REST API: PIV card supplement
            API Reference

### Finding the PIV card type

### Finding the URL to create cardholders

### Finding the URL of a cardholder

### Licensing

### Cardholder API changes in 8.10

This document is a supplement to the
                  Cardholder API documentation ('Cardholder' is the Command Centre term for a user). That documentation describes how to add a card to an existing cardholder in a
                  PATCH, and how to create a cardholder plus cards in a
                  POST, but in the interests of brevity its examples do not cover PIV.

The Schema Definitions section gives you the details of the PIV part of a Command Centre card, with examples of what you would submit to
                  create one or
                  update one, and what Command Centre will send you when you
                  view one.

The Paths section takes those schema examples and wraps them into a sample
                  PATCH and
                  POST. The two differ only in how they wrap the card into the submission body.

If your application will be assigning PIV cards to cardholders, however, the first thing it needs to do is learn the value of some constants for your particular installation of Command Centre.

When you assign any card to a cardholder, PIV or otherwise, you need to provide the identifier of the card type. It will vary between Command Centre installations, so you cannot use a value from another installation or these examples. It will not change while Command Centre is running but it may change at upgrade, so your application should follow this process at startup.

It takes two queries and a loop:

You can accomplish the last two steps with the JSONPath filter

$.results[?(@.credentialClass=='piv')].href.

Explanation: Command Centre ships with a handful of card types, and administrators can add more, but the one that Command Centre uses for PIV and PIV-I cards has its own credential class. It will look like
                  the example below.

GET /api. The link is at features.cardholders.cardholders.href.

See the main cardholder documentation, particularly the section on
                  searching cardholders.

All of the API calls described here require the RESTCardholders licence. If your site is also licensed to use PIV cards, you can access them via REST.

- GET /api,

- if running 8.00 or earlier, follow the link at features.cardTypes.cardTypes.href (which will be to /api/card_types), or

- if running 8.10 or later, follow the link at features.cardTypes.assign.href (which will probably be to /api/card_types/assign. Both URLs will work in 8.10, but the advantage of this URL is that your operator can access it with fewer privileges),

- iterate through the array to find the element with credentialClass:  piv, and

- note its href.

- Certificates and biometric data are now available without a customisation. They are too large to send to all REST clients so you must ask for them using the fields parameter.

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

This is the first call your application should make. It contains the URLs of every other call. The one you need for creating PIV cards is an href in features.cardTypes.cardTypes.

Success

The site does not have a REST licence.

Creates a new cardholder, including assigned cards.

Part of what you submit is an array called cards, each element of which is a card or credential you want assigned to your new cardholder. This example shows an array containing one PIV card.

See the
                    PIV card create schema definition for the PIV-specific fields. The fields that are not specific to PIV and PIV-I cards are fully documented
                    in the cardholder API. One, division, is mandatory, and you must also supply either a first or last name for your new cardholder.

Mandatory when creating any cardholder.

Optional. See
                          the cardholder API documentation.

**division:
object:** Mandatory when creating any cardholder.

**firstName:
string:** Optional. See
                          the cardholder API documentation.

Success.

Updates an existing cardholder, including adding, removing, and modifying cards.

Part of what you submit is an object called cards, described below.

The other fields you can pass in this call, such as personal information, access rights, and card data such as issue levels, status, and validity to/from dates, are fully documented in the
                    main cardholder API. This example shows how to assign a PIV card to a cardholder, and how to change the two status fields of an existing PIV card.

See
                          the cardholder API documentation. There is a lot you can do here.

cards can contain three arrays called add, update, and delete.

An element in the add array is the same as you would supply to a
                          POST when creating a cardholder with cards. There is an example here. See the
                          PIV card update schema definition for the PIV-specific field definitions.

An element in the update array is much smaller, because the only PIV data you can change is the PIV status. This example changes the PIV status of a card card to 'notChecked', which would normally activate it, but in this example we also disable the card. Presumably we do not want this person using the card just yet.

Deleting a PIV card is no different from
                          deleting any other kind of credential.

**firstName:
string:** See
                          the cardholder API documentation. There is a lot you can do here.

**cards:
object:** cards can contain three arrays called add, update, and delete.
An element in the add array is the same as you would supply to a
                          POST when creating a cardholder with cards. There is an example here. See the
                          PIV card update schema definition for the PIV-specific field definitions.
An element in the update array is much smaller, because the only PIV data you can change is the PIV status. This example changes the PIV status of a card card to 'notChecked', which would normally activate it, but in this example we also disable the card. Presumably we do not want this person using the card just yet.
Deleting a PIV card is no different from
                          deleting any other kind of credential.

Success.

You will receive this URL from other calls to the API, in a search result or an access group membership list, for example. The URL is the unique identifier of a cardholder, and calling it returns much of what Command Centre holds for that person, including assigned cards.

The result includes an array called cards. Each element of that array is a credential of some type. If it is a PIV card, it will look like the example here.

Note that this example is an array of one.

The
                    PIV card GET schema is below. The
                    main documentation covers all the other fields Command Centre holds for a cardholder.

The ID of the cardholder.

Success.

The operator does not have a privilege that allows reading cardholders, or there is no cardholder at that address.

In addition to the generic card data the API always returns in the cards section (in the
                    Cardholders API document) of a cardholder detail page, including href, number, issueLevel, type, and status, PIV cards have a pivData structure.

In version 8.00 and later, credentialClass will be 'piv'.

There is no difference between the href of a PIV card and any other. DELETE it to delete a card. That is the only verb you can use on this URL. GET will always return a 404.

Do not specify it when creating a card.

While the card number rules on a PIV card type are different from those on non-PIV card types, card numbers are still strings, and the API will accept them from you and return them to you in the same way.

PIV card numbers are the FASC-N with hyphens splitting the major components.

PIV-I card numbers are the CHUID's GUID in decimal.

A PIV card's issue level is derived from data in the CHUID block, presented here for convenience.

PIV cards have more inactive card states than other cards. 'Time Invalid (PIV certificate)' or 'Not Trusted', for example. If status.type is 'inactive', status.value will give an indication why.

The status of a new card will be 'revoked' if you set the PIV status (which is different from the card status, note) to anything but 'normal' or 'offline'.

This is much like the
                        pivData you send when creating a card, described later, except that:

in certain cases (described below) it does not contain the certificates and biometrics, leaving just the
                            CHUID block (a subset of the card's CHUID), and the PIV status.

it adds lastCheckTime, which shows when the card was last validated. That is most useful when Command Centre is performing periodic card validation. If you are doing your own validation, this field will hold the moment you last updated the card.

If the site is running 7.90 and the RESTPIVCardExport customisation, or if it is running a later version and you asked for extra data using the fields query parameter, the pivData section will contain three certificates and the fingerprint templates in the correct format for import into another installation of Command Centre.

The certificates and biometrics are missing from the default result set in the interest of efficiency: those fields are very large, and most of our integrations do not require them. Some are running over cellular networks, where bandwidth is carries a cost. Omitting the large fields by default but sending them on request is a flexible compromise that allows integrators to build a suitable system without wastage.

The type field in this block contains the PIV-specific status of the card, as opposed to the generic credential state which is in a block called status at the same level as pivData.

The
                              schema for PIV card data lists the values it might come back with.

Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.

This example is shortened to fit on screen. Real certificates are at least a thousand characters.

This contains the CAK, which is necessary for the secure use of contactless cards. It is not required for contact cards.

Optional when creating a PIV or PIV-I card. The API will reject your request if this is present and not a Base64-encoded certificate, but it will not validate the certificate itself.

Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.

If you send this when creating a card, it should be the cardholder's fingerprints contained in the card's Cardholder Fingerprints data object with the error detection code removed: the whole CBEFF structure including the CBEFF_HEADER, CBEFF_BIOMETRIC_RECORD, and CBEFF_SIGNATURE_BLOCK components. Refer to Section 9 of NIST Special Publication 800-76-2: Biometric Data Specification for Personal Identity Verification.

It is optional. The API will check that this is Base64, but will not verify that it is valid biometric data.

- in certain cases (described below) it does not contain the certificates and biometrics, leaving just the
                            CHUID block (a subset of the card's CHUID), and the PIV status.

- it adds lastCheckTime, which shows when the card was last validated. That is most useful when Command Centre is performing periodic card validation. If you are doing your own validation, this field will hold the moment you last updated the card.

**href:
string
(url):** There is no difference between the href of a PIV card and any other. DELETE it to delete a card. That is the only verb you can use on this URL. GET will always return a 404.
Do not specify it when creating a card.

**number:
string:** While the card number rules on a PIV card type are different from those on non-PIV card types, card numbers are still strings, and the API will accept them from you and return them to you in the same way.
PIV card numbers are the FASC-N with hyphens splitting the major components.
PIV-I card numbers are the CHUID's GUID in decimal.

**issueLevel:
integer:** A PIV card's issue level is derived from data in the CHUID block, presented here for convenience.

**status:
object:** PIV cards have more inactive card states than other cards. 'Time Invalid (PIV certificate)' or 'Not Trusted', for example. If status.type is 'inactive', status.value will give an indication why.
The status of a new card will be 'revoked' if you set the PIV status (which is different from the card status, note) to anything but 'normal' or 'offline'.

**pivData:
object:** This is much like the
                        pivData you send when creating a card, described later, except that:


in certain cases (described below) it does not contain the certificates and biometrics, leaving just the
                            CHUID block (a subset of the card's CHUID), and the PIV status.


it adds lastCheckTime, which shows when the card was last validated. That is most useful when Command Centre is performing periodic card validation. If you are doing your own validation, this field will hold the moment you last updated the card.


If the site is running 7.90 and the RESTPIVCardExport customisation, or if it is running a later version and you asked for extra data using the fields query parameter, the pivData section will contain three certificates and the fingerprint templates in the correct format for import into another installation of Command Centre.
The certificates and biometrics are missing from the default result set in the interest of efficiency: those fields are very large, and most of our integrations do not require them. Some are running over cellular networks, where bandwidth is carries a cost. Omitting the large fields by default but sending them on request is a flexible compromise that allows integrators to build a suitable system without wastage.

**chuid:


CHUID





pivStatus:
object



The type field in this block contains the PIV-specific status of the card, as opposed to the generic credential state which is in a block called status at the same level as pivData.
The
                              schema for PIV card data lists the values it might come back with.


lastCheckTime:
string
(date-time)



contentSigningCert:
string



Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.
This example is shortened to fit on screen. Real certificates are at least a thousand characters.


cardAuthenticationCert:
string



This contains the CAK, which is necessary for the secure use of contactless cards. It is not required for contact cards.
Optional when creating a PIV or PIV-I card. The API will reject your request if this is present and not a Base64-encoded certificate, but it will not validate the certificate itself.


pivAuthenticationCert:
string



Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.


fingerprints:
string



If you send this when creating a card, it should be the cardholder's fingerprints contained in the card's Cardholder Fingerprints data object with the error detection code removed: the whole CBEFF structure including the CBEFF_HEADER, CBEFF_BIOMETRIC_RECORD, and CBEFF_SIGNATURE_BLOCK components. Refer to Section 9 of NIST Special Publication 800-76-2: Biometric Data Specification for Personal Identity Verification.
It is optional. The API will check that this is Base64, but will not verify that it is valid biometric data.:** The type field in this block contains the PIV-specific status of the card, as opposed to the generic credential state which is in a block called status at the same level as pivData.
The
                              schema for PIV card data lists the values it might come back with.

**chuid:


CHUID:** Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.
This example is shortened to fit on screen. Real certificates are at least a thousand characters.

**pivStatus:
object:** This contains the CAK, which is necessary for the secure use of contactless cards. It is not required for contact cards.
Optional when creating a PIV or PIV-I card. The API will reject your request if this is present and not a Base64-encoded certificate, but it will not validate the certificate itself.

**lastCheckTime:
string
(date-time):** Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.

**contentSigningCert:
string:** If you send this when creating a card, it should be the cardholder's fingerprints contained in the card's Cardholder Fingerprints data object with the error detection code removed: the whole CBEFF structure including the CBEFF_HEADER, CBEFF_BIOMETRIC_RECORD, and CBEFF_SIGNATURE_BLOCK components. Refer to Section 9 of NIST Special Publication 800-76-2: Biometric Data Specification for Personal Identity Verification.
It is optional. The API will check that this is Base64, but will not verify that it is valid biometric data.

**chuid:


CHUID:** The type field in this block contains the PIV-specific status of the card, as opposed to the generic credential state which is in a block called status at the same level as pivData.
The
                              schema for PIV card data lists the values it might come back with.

**pivStatus:
object:** Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.
This example is shortened to fit on screen. Real certificates are at least a thousand characters.

**lastCheckTime:
string
(date-time):** This contains the CAK, which is necessary for the secure use of contactless cards. It is not required for contact cards.
Optional when creating a PIV or PIV-I card. The API will reject your request if this is present and not a Base64-encoded certificate, but it will not validate the certificate itself.

**contentSigningCert:
string:** Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.

**cardAuthenticationCert:
string:** If you send this when creating a card, it should be the cardholder's fingerprints contained in the card's Cardholder Fingerprints data object with the error detection code removed: the whole CBEFF structure including the CBEFF_HEADER, CBEFF_BIOMETRIC_RECORD, and CBEFF_SIGNATURE_BLOCK components. Refer to Section 9 of NIST Special Publication 800-76-2: Biometric Data Specification for Personal Identity Verification.
It is optional. The API will check that this is Base64, but will not verify that it is valid biometric data.

When you
                    collect card types from Command Centre in preparation for giving a PIV card to someone, the card type you seek will look like this.

The number in this URL may be different on your system.

All credential types have a set of card states. PIV cards have two.

You are after the card type with credential class 'piv'.

**href:
string
(url):** The number in this URL may be different on your system.

**name:
string:** All credential types have a set of card states. PIV cards have two.

**availableCardStates:
string[]:** You are after the card type with credential class 'piv'.

This example, when placed inside the cards array of a
                    POST or the cards.add array of a
                    PATCH, would create a PIV card credential and assign it to the cardholder created by the POST or identified by the address of the PATCH. Consult those methods for the JSON you need to wrap around this example.

A PIV-I example would have a different style of FASC-N, and a card number to match.

Required.

For a PIV card, this is the FASC-N.

For a PIV-I card, this is the decimal representation of the GUID in the CHUID, and must not be the same as the FASC-N.

The server ignores hyphens here, so you can send back the same number you received from a GET.

Optional, but when controlling physical access you should be explicit rather than relying on a default. Set the value field inside it to either 'active' or 'disabled (manually)'. Case insensitive.

You will not be able to activate a card if its PIV status (inside the pivdata block) prevents it.

Required. This should be the href of the built-in PIV/PIV-I card type, that your application found using the process
                        in the introduction.

This block contains all the PIV-specific fields. Everything outside this block, including the number, status, and type, is common to all Command Centre cards and credentials.

**number:
string:** Required.
For a PIV card, this is the FASC-N.
For a PIV-I card, this is the decimal representation of the GUID in the CHUID, and must not be the same as the FASC-N.
The server ignores hyphens here, so you can send back the same number you received from a GET.

**status:
object:** Optional, but when controlling physical access you should be explicit rather than relying on a default. Set the value field inside it to either 'active' or 'disabled (manually)'. Case insensitive.
You will not be able to activate a card if its PIV status (inside the pivdata block) prevents it.

**type:
object:** Required. This should be the href of the built-in PIV/PIV-I card type, that your application found using the process
                        in the introduction.

**pivData:


PIV card data:** This block contains all the PIV-specific fields. Everything outside this block, including the number, status, and type, is common to all Command Centre cards and credentials.

This example, when placed inside cards.update array of a PATCH, would update a card. The main cardholder API documentation shows where to send the PATCH and what else you can change with it. This example only shows how to update the status (common to all credential types) and the PIV data.

Specifically, the PIV status, because that is the only PIV-specific data you can change on a PIV card. Everything else--the CHUID, certificates, and biometrics--are all fixed, once set.

See the
                    PIV data schema for when you should set the PIV status and what you can set it to.

This is the href of the card you want to update, found in the cards array in the
                        cardholder detail.

Optional. If you omit both this and the PIV status, or omit this and set the PIV status to 'normal' or 'offline', the server will set this to 'active'. Any other PIV status will cause the card to become inactive, regardless of what you put here.

So you really only need to set this when you are changing the PIV status to 'normal' or 'offline' but you want the card to remain disabled. In that case, set status.value to 'disabled (manually)'.

This can contain only one field when you are updating a PIV card, pivStatus.type.

The
                        PIV card data schema lists the values you can send.

**href:
string
(url):** This is the href of the card you want to update, found in the cards array in the
                        cardholder detail.

**status:
object:** Optional. If you omit both this and the PIV status, or omit this and set the PIV status to 'normal' or 'offline', the server will set this to 'active'. Any other PIV status will cause the card to become inactive, regardless of what you put here.
So you really only need to set this when you are changing the PIV status to 'normal' or 'offline' but you want the card to remain disabled. In that case, set status.value to 'disabled (manually)'.

**pivData:
object:** This can contain only one field when you are updating a PIV card, pivStatus.type.
The
                        PIV card data schema lists the values you can send.

This example, when placed inside one of the cards in the cards array of a POST or the cards.add array of a PATCH, would create a PIV card. It contains all the PIV-specific fields on a credential.

It is the same as the block you
                    receive from the API for an existing PIV card, minus lastCheckTime.

Required. The FASC-N in the chuid block, and its relationship with the card number, is the only difference between PIV and PIV-I cards in Command Centre.

This PIV status is distinct from the card status, which is a different field outside the pivData block.

It is optional. If you omit it when creating a card, the card will be enabled with a status of "NotChecked", which is a perfectly valid operating status. If Command Centre is doing periodic certificate validation, the status will eventually change (to 'normal', all going well).

You should send this block only if you are doing your own certificate validation. It must contain a field type set to one of these values, based on the result of your validation:

If you set it to any value except 'NotChecked, 'Normal', or 'Offline', Command Centre will consider the card invalid and deactivate it regardless of what you pass as the card status. You, or Command Centre's periodic certificate validation, may validate the card again later.

Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.

This example is shortened to fit on screen. Real certificates are at least a thousand characters.

This contains the CAK, which is necessary for the secure use of contactless cards. It is not required for contact cards.

Optional when creating a PIV or PIV-I card. The API will reject your request if this is present and not a Base64-encoded certificate, but it will not validate the certificate itself.

Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.

If you send this when creating a card, it should be the cardholder's fingerprints contained in the card's Cardholder Fingerprints data object with the error detection code removed: the whole CBEFF structure including the CBEFF_HEADER, CBEFF_BIOMETRIC_RECORD, and CBEFF_SIGNATURE_BLOCK components. Refer to Section 9 of NIST Special Publication 800-76-2: Biometric Data Specification for Personal Identity Verification.

It is optional. The API will check that this is Base64, but will not verify that it is valid biometric data.

- Normal

- Offline

- Revoked

- Expired

- CertInChainRevoked

- CertInChainExpired

- IssuerSigCertRevoked

- IssuerSigCertExpired

- NotTrusted

- PolicyError

- OtherError

- NotChecked

**chuid:


CHUID:** Required. The FASC-N in the chuid block, and its relationship with the card number, is the only difference between PIV and PIV-I cards in Command Centre.

**pivStatus:
object:** This PIV status is distinct from the card status, which is a different field outside the pivData block.
It is optional. If you omit it when creating a card, the card will be enabled with a status of "NotChecked", which is a perfectly valid operating status. If Command Centre is doing periodic certificate validation, the status will eventually change (to 'normal', all going well).
You should send this block only if you are doing your own certificate validation. It must contain a field type set to one of these values, based on the result of your validation:

Normal
Offline
Revoked
Expired
CertInChainRevoked
CertInChainExpired
IssuerSigCertRevoked
IssuerSigCertExpired
NotTrusted
PolicyError
OtherError
NotChecked

If you set it to any value except 'NotChecked, 'Normal', or 'Offline', Command Centre will consider the card invalid and deactivate it regardless of what you pass as the card status. You, or Command Centre's periodic certificate validation, may validate the card again later.

**contentSigningCert:
string:** Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.
This example is shortened to fit on screen. Real certificates are at least a thousand characters.

**cardAuthenticationCert:
string:** This contains the CAK, which is necessary for the secure use of contactless cards. It is not required for contact cards.
Optional when creating a PIV or PIV-I card. The API will reject your request if this is present and not a Base64-encoded certificate, but it will not validate the certificate itself.

**pivAuthenticationCert:
string:** Required when creating a PIV or PIV-I card. The API will reject your request if this is missing or not a Base64-encoded certificate, but it will not validate the certificate itself.

**fingerprints:
string:** If you send this when creating a card, it should be the cardholder's fingerprints contained in the card's Cardholder Fingerprints data object with the error detection code removed: the whole CBEFF structure including the CBEFF_HEADER, CBEFF_BIOMETRIC_RECORD, and CBEFF_SIGNATURE_BLOCK components. Refer to Section 9 of NIST Special Publication 800-76-2: Biometric Data Specification for Personal Identity Verification.
It is optional. The API will check that this is Base64, but will not verify that it is valid biometric data.

This is an example CHUID block for a PIV card. The only difference between this and a PIV-I card is the FASC-N.

This is the hash of the CHUID object, Base64-encoded. For a 256-bit hash it should be 44 characters long including one = pad. The API will reject a string that is not valid Base64, but it will not verify the hash.

Required in versions up to 8.60. Optional in 8.70 and later.

The FASC-N identifier. Required.

It must be the same as the card number on a PIV (not PIV-I) card.

On a PIV-I card, it must not be the same as the card number (and for Federal PIV-I cards it will likely begin with fourteen nines).

Optional.

Optional.

**hash:
string:** This is the hash of the CHUID object, Base64-encoded. For a 256-bit hash it should be 44 characters long including one = pad. The API will reject a string that is not valid Base64, but it will not verify the hash.
Required in versions up to 8.60. Optional in 8.70 and later.

**fascn:
string:** The FASC-N identifier. Required.
It must be the same as the card number on a PIV (not PIV-I) card.
On a PIV-I card, it must not be the same as the card number (and for Federal PIV-I cards it will likely begin with fourteen nines).

**orgIdentifier:
string:** Optional.

**duns:
string:** Optional.
