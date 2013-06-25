**Warning: This is an alpha version of the RIQ API and we don't yet make commitments to preserve backwards compatibility.**

Entity Lists
============

## Endpoints

[Get Lists](#get-lists)

[Search Relationships](#search-relationships)

[Add Relationship](#add-relationship)

[Set Field Values](#set-field-values)

[Create Comment Event](#create-comment-event)

## Get Lists

### Definition

```
GET https://www.relateiq.com/api/v1/entitylists/{listids}
```

### Arguments

#### Optional

**listids** - comma separated list of ids, if none are specified, this returns all lists.

**excludeMembers** - set to 'true' to not return list members, this is preferred as it is much faster and smaller.

**lastModified** - only returns lists modified since this date (represented as millis since epoch)

### Request Examples

```bash
curl -X GET https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788,3ebd05393434b301d30a6788 \
  -u apitoken:[apitoken] \
  -d "excludeMembers=true"
```

### Response Example

```javascript
{
    result: {[{
            "id": "3ebd05233434b301d30b6788",
            "title": "List 1",
            "fields": [...],
            ...
        },{
            "id": "3ebd05393434b301d30a6788",
            "title": "List 2",
            "fields": [...],
            ...
        }
    ]},
    success: true
}
```

### Error Example

```javascript
{
    result: {}
    message: "invalid list specified"
    success: false
}
```

## Search Relationships

### Definition

```
GET https://www.relateiq.com/api/v1/entitylists/{listid}/search?name=[name to find]
```

### Arguments

#### Required (part of resource url)

**listid** - the list id within which to search

#### Required (query parameters)

**name** - the name to search for

Currently, the only property that is searchable is the name.

### Request Examples

```bash
curl -X GET https://www.relateiq.com/api/v1/entitylist/3ebd05233434b301d30b6788/search?name=techqueria \
  -u apitoken:[apitoken]
```

### Response Example

```javascript
{
    result: {[{
            "id": "3ebd05233434b301d30b6788",
            "memberName": "Techqueria",
            "fieldValuess": [...],
            ...
        }
    ]},
    success: true
}
```

### Error Example

```javascript
{
    result: {}
    message: "must specify a query (e.g. name=techqueria)"
    success: false
}
```

## Add Relationship

### Definition

```
POST https://www.relateiq.com/api/v1/entitylists/{listid}/addrelationship
```

### Arguments

#### Required

**listid** - The id of the list you want to modify. one easy way to find this is to navigate to the list in the web app. The listid is in the url (e.g. https://www.relateiq.com/#list:l= **3ebd05233434b301d30b6788**).

**firstName** - The first (or full) name of the contact to add. This is used if there is no existing contact for the given email address or phone number.

#### Contact Details (at least one required)

**email** - The email address of the contact.

**phone** - The phone number of the contact.

#### Optional

**lastName** - The last name of the contact to add.

**relationshipName** - If the relationship represents a company, this is the company name.


### Request Examples

```bash
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

```javascript
{
    result: ["Created a contact", "Added a member"]
    success: true
}
```

### Error Example

```javascript
{
    result: {}
    message: "invalid name specified"
    success: false
}
```

## Set Field Values

### Definition

```
POST https://www.relateiq.com/api/v1/[listid]/fieldValues
```

### Arguments

#### Required (json document)

**data** - An array of objects that represent existing relationships on the list, where the properties of this object represent the values you want to use to match to existing relationships and the values you want to set.

**matchBy** - Can be “id”, “email”, or one of the field ids in the list (e.g. “3”,“10”, etc. you can find field ids by querying the Fields api endpoint). This value chooses which property of the data array objects is used to match to existing relationships.

### Response

All responses are returned as JSON documents, wrapped in a standard formatted envelope that contains a result object, a boolean representing success (false means failure), and optionally a message describing the failure.

### Request Examples

#### Set Field “10” (e.g. Custom Unique Identifier) For 2 Existing Relationships, Finding Them By The Riq Relationship Id

```bash
curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/addrelationship \
-u apitoken:[apitoken] \
-H 'Content-Type: application/json' \
-d '{   "matchBy": "id",
    "data": [
{"id":"3f31eb19f5b0fe947bb774fd", "10":"myexternalid1"},
{"id":"3fd0e101e4b0df313520bc9a", "10":"myexternalid2"}
]}'
```

#### Set Field “11” (e.g.contract Value) For 2 Existing Relationships, Finding Them By My Custom Unique Identifier

```bash
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

```javascript
{
    "result": {
        "notFoundMatches": ["myexternalid1"],
        "updatedIds": ["3f31eb19e4b0fd947bb774fd"],
        "unchangedIds": []
    },
    "message": null,
    "success": true
}
```

### Error Example

```javascript
{
    result: {}
    message: "Invalid list specified"
    success: false
}
```

## Create Comment Event

### Definition

```
POST https://www.relateiq.com/api/v1/[listid]/commentbyemail
```

### Arguments

#### Required

**relationshipemail** - The email address to use to find relationships in the specified list, all matches will receive the comment event.

**body** - The body of the comment event.

**authoremailoverride** - By default, the user whose apitoken is used will be the author of this comment/event. You may override it with this parameter.

**timeoverride** - By default, the current time will be used. You may override it by setting this parameter to milliseconds since epoch.

### Response

All responses are returned as JSON documents, wrapped in a standard formatted envelope that contains a result object, a boolean representing success (false means failure), and optionally a message describing the failure.

### Request Examples

```bash
curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/commentbyemail \
    -u apitoken:[apitoken] \
    -d relationshipemail="test@test.com" \
    -d body="some new comment" \
    -d authoremailoverride="author@company.com" \
    -d timeoverride=1354907499000
```

### Response Example

#### All events that are created are returned

```javascript
{
    "result": [{
        "contributionType": "All",
        "props": {
            "uid": "3e9ccaf434347446186680cb",
            "body": "some new comment\n\n(created via API by Your User Name)"
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

```javascript
{
    result: {}
    message: "no comments created"
    success: false
}
```
