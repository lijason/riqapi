Entity Lists
------------

REST API

Add Relationship
DEFINITION
POST https://www.relateiq.com/api/v1/entitylists/[listid]/addrelationship

ARGUMENTS
Required
listid - the id of the list you want to modify. the easiest way to find this is to click on the list in the web app and pull it from the url: https://www.relateiq.com/#list:l=3ebd05233434b301d30b6788
firstName - the first (or full) name of the contact to add

Optional
lastName - the last name of the contact to add
relationshipName - if the relationship represents a company
email - the email address of the contact
phone - the phone number of the contact

REQUEST EXAMPLES
curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/addrelationship \
  -u apitoken:[apitoken] \
  -d “firstName=John” \
  -d “lastName=Doe” \
  -d “email=johndoe@relateiq.com" \
  -d “phone=555-1212"

curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/addrelationship \
  -u apitoken:[apitoken] \
  -d “firstName=John Doe” \
  -d “phone=555-1212"

RESPONSE EXAMPLE
{
    result: ["Created a contact", "Added a member"]
    success: true
}
ERROR EXAMPLE
{
    result: {}
    message: "invalid name specified"
    success: false
}


Set Field Values
DEFINITION
POST https://www.relateiq.com/api/v1/[listid]/fieldValues

ARGUMENTS
Required (json document)
data - an array of objects that represent existing relationships on the list, where the properties of this object represent the values you want to use to match to existing relationships and the values you want to set.
matchBy - can be “id”, “email”, or one of the field ids in the list (e.g. “3”,“10”, etc. you can find field ids by querying the Fields api endpoint) This value chooses which property of the data array objects is used to match to existing relationships.

RESPONSE
All responses are returned as JSON documents, wrapped in a standard formatted envelope that contains a result object, a boolean representing success (false means failure), and optionally a message describing the failure.


REQUEST EXAMPLES

Set field “10” (e.g. Custom Unique Identifier) for 2 existing relationships, finding them by the RIQ relationship ID

curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/addrelationship \
-u apitoken:[apitoken] \
-H 'Content-Type: application/json' \
-d '{   "matchBy": "id",
    "data": [
{"id":"4f31eb19f5b0fe947bb774fd", "10":"myexternalid1"},
{"id":"4fd0e101e4b0df313520bc9a", "10":"myexternalid2"}
]}'

Set field “11” (e.g.Contract Value) for 2 existing relationships, finding them by my Custom Unique Identifier

curl -X POST https://www.relateiq.com/api/v1/entitylists/3ebd05233434b301d30b6788/addrelationship \
-u apitoken:[apitoken] \
-H 'Content-Type: application/json' \
-d '{   "matchBy": "10",
    "data": [
{"10":"myexternalid1", “11”: “30000”},
{"10":"myexternalid2", “11”:”40000”}
]}'


RESPONSE EXAMPLE
{
    result: ...
    success: true
}
ERROR EXAMPLE
{
    result: {}
    message: "invalid name specified"
    success: false
}