Entity Lists
============

## Endpoints

[Add Relationship](#addrelationship)

[Set Field Values](#setfieldvalues)

[Create Comment Event](#createcomment)

<a name="addrelationship"/>
## Add Relationship

### Definition

```
POST https://www.relateiq.com/api/v1/entitylists/{listid}/addrelationship
```

### Arguments

#### Required

**listid** - the id of the list you want to modify. the easiest way to find this is to click on the list in the web app and pull it from the url (e.g. https://www.relateiq.com/#list:l= **3ebd05233434b301d30b6788**)

**firstName** - the first (or full) name of the contact to add

#### Optional
**lastName** - the last name of the contact to add

**relationshipName** - if the relationship represents a company

**email** - the email address of the contact

**phone** - the phone number of the contact

### Request Examples

```javascript
curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/addrelationship \
  -u apitoken:[apitoken] \
  -d "firstName=John" \
  -d "lastName=Doe" \
  -d "email=johndoe@relateiq.com" \
  -d "phone=555-1212"

curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/addrelationship \
  -u apitoken:[apitoken] \
  -d "firstName=John Doe" \
  -d "phone=555-1212"
```

### Response Example

```
{
    result: ["Created a contact", "Added a member"]
    success: true
}
```

### Error Example

```
{
    result: {}
    message: "invalid name specified"
    success: false
}
```

<a name="setfieldvalues"/>
## Set Field Values

### Definition

```
POST https://www.relateiq.com/api/v1/[listid]/fieldValues
```

### Arguments

#### Required (json document)

**data** - an array of objects that represent existing relationships on the list, where the properties of this object represent the values you want to use to match to existing relationships and the values you want to set.

**matchBy** - can be “id”, “email”, or one of the field ids in the list (e.g. “3”,“10”, etc. you can find field ids by querying the Fields api endpoint) This value chooses which property of the data array objects is used to match to existing relationships.

### Response

All responses are returned as JSON documents, wrapped in a standard formatted envelope that contains a result object, a boolean representing success (false means failure), and optionally a message describing the failure.

### Request Examples

#### Set Field “10” (e.g. Custom Unique Identifier) For 2 Existing Relationships, Finding Them By The Riq Relationship Id

```javascript
curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/addrelationship \
-u apitoken:[apitoken] \
-H 'Content-Type: application/json' \
-d '{   "matchBy": "id",
    "data": [
{"id":"4f31eb19f5b0fe947bb774fd", "10":"myexternalid1"},
{"id":"4fd0e101e4b0df313520bc9a", "10":"myexternalid2"}
]}'
```

#### Set Field “11” (e.g.contract Value) For 2 Existing Relationships, Finding Them By My Custom Unique Identifier

```javascript
curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/addrelationship \
-u apitoken:[apitoken] \
-H 'Content-Type: application/json' \
-d '{"matchBy": "10",
    "data": [
        {"10":"myexternalid1", “11”: “30000”},
        {"10":"myexternalid2", “11”:”40000”}
    ]}'
```

### Response Example

```
{
    "result": {
        "notFoundMatches": ["myexternalid1"],
        "updatedIds": ["4f31eb19e4b0fd947bb774fd"],
        "unchangedIds": []
    },
    "message": null,
    "success": true
}
```

### Error Example

```
{
    result: {}
    message: "Invalid list specified"
    success: false
}
```

<a name="createcomment"/>
## Create Comment Event

### Definition

```
POST https://www.relateiq.com/api/v1/[listid]/commentbyemail
```

### Arguments

#### Required

**relationshipemail** - The email address to use to find relationships in the specified list, all matches will receive the comment event

**body** - The body of the comment event

**authoremailoverride** - By default, the user whose apitoken is used will be the author of this comment/event. You may override it with this parameter.

**timeoverride** - By default, the current time will be used. You may override it by setting this parameter to milliseconds since epoch.

### Response

All responses are returned as JSON documents, wrapped in a standard formatted envelope that contains a result object, a boolean representing success (false means failure), and optionally a message describing the failure.

### Request Examples

```javascript
curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/commentbyemail \
    -u apitoken:[apitoken] \
    -d relationshipemail="test@test.com" \
    -d body="some new comment" \
    -d authoremailoverride="author@company.com" \
    -d timeoverride=1354907499000
```

### Response Example

#### All events that are created are returned

```
{
    "result": [{
        "contributionType": "All",
        "props": {
            "uid": "3e9ccaf434347446186680cb",
            "body": "abc\n\n(created via API by Your User Name)"
        },
        "mine": false,
        "maybeMine": false,
        "bodyResourceId": null,
        "fromKid": null,
        "toKids": [],
        "cckids": [],
        "bcckids": [],
        "prettyType": "comment",
        "eventDisplayMode": "Mini",
        "future": false,
        "unread": false,
        "type": "comment",
        "time": 1354907499000,
        "listId": "3ebd05233434b301d30b6788",
        "memberId": "31310afbe4b0e853408c1c9f",
        "keyIds": ["3ec0b63c3434819b92dcfda0"],
        "userContributions": [],
        "originalType": null,
        "id": null
    }],
    "message": null,
    "success": true
}
```

### Error Example

#### For example, if no relationship is found with relationshipemail, you will receive:

```
{
    result: {}
    message: "no comments created"
    success: false
}
```
