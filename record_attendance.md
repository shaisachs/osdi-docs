---
layout: default
title: Record Attendance Helper
---

# Record Attendance Helper

This document defines the Record Attendance Helper resource. 

The Record Attendance Helper is a helper endpoint to aid in the creation of [Attendance](attendances.html) and [People](people.html) resources via POST. It provides a quick and easy way to create attendances and people at the same time, eliminating the need for multiple POST operations to store that information.

Some systems may attempt to match people sent via the Record Attendance Helper to existing people in the database and update their record instead of creating a new person. The method used for matching will be detailed in that system's documentation. 

When using the Record Attendance Helper, tagging and list membership info may be added at the same time as well, eliminating the need for multiple POST operations to store that information.

In addition, you can indicate to the server whether to trigger additional actions, such as an autoresponse email sent back to the person who took action.

Typically, the response to a successful Record Attendance Helper POST is the full representation of the attendance. However, the Record Attendance Helper can be used without authentication, allowing for use in frontend javascript-based applications without giving away API key secrets, for example. If no authentication is passed, the response will simply be the server response code, to avoid leaking any data. (ex: 200 for success, 500 for error, etc...)

Some initial implementations may only support helpers -- direct RESTful access may not be supported. In those cases, the _links section may be omitted in responses.


### Sections:

* [Endpoints and URL structures](#endpoints-and-url-structures)
* [Fields](#fields)
	* [Common Fields](#common-fields)
    * [Record Attendance Helper Fields](#record-attendance-helper-fields)
    * [Related Objects](#related-objects)
* [Related Resources](#related-resources)
* [Scenarios](#scenarios)
    * [Scenario: Creating a new attendance and person (POST)](#scenario-creating-a-new-attendance-and-person-post)
    * [Scenario: Creating a new attendance and person without authentication (POST)](#scenario-creating-a-new-attendance-and-person-without-authentication-post)


{% include endpoints_and_url_structures.md %}

The link relation label for the Record Attendance Helper is ```osdi:record_attendance_helper```.

_[Back to top...](#)_


## Fields

{% include fields_intro.md %}

{% include global_fields_helper.md %}

_[Back to top...](#)_


### Record Attendance Helper Fields

A list of fields specific for POSTing via the Record Attendance Helper.

| Name          | Type      | Description
|-----------    |-----------|-----------|--------------
|origin_system		|string     |A human readable identifier of the system where this attendance was created. (ex: "OSDI System")
|action_date		|string		|The date and time the attendance was made by the person.
|status			|enum			|The attendee's response status. One of "declined", "tentative", "accepted", or "needs action".
|attended		|boolean		|Represents whether the person actually attended the event or not.
|comment		|string			|An optional comment from the attendee.
|add_tags      |strings[]     |An array of tag names corresponding to previously created tags to add to this person when it is created.
|add_lists     |strings[]     |An array of list names corresponding to previously created lists to add to this person when it is created.
|person			|[Person*](#person)	|An object hash representing the person who made the attendance.
|triggers		|[Triggers](#triggers)	|An object hash representing responses a user would like to trigger from the server as part of the POST, such as sending an autoresponse email back to the person who took action with this helper.

_[Back to top...](#)_


### Related Objects

These JSON hashes included in the table above are broken out into their own tables for readability, rather than independent resources with their own endpoints.

#### Person

|Name          |Type      |Description
|-----------    |-----------|--------------
|person      |[Person*](people.html)     |An inlined hash representation of a person, containing any valid fields for the Person resource.


#### Triggers

|Name          |Type      |Description
|-----------    |-----------|--------------
|autoresponse   |object     |An object hash representing the autoresponse email trigger type.
|autoresponse.enabled   |boolean     |A boolean indicating whether the autoresponse email should be sent or not.

_[Back to top...](#)_


## Related Resources

* [Attendance](attendances.html)
* [Person](people.html)

_[Back to top...](#)_


## Scenarios

{% include scenarios_helper_intro.md %}


### Scenario: Creating a new attendance and person (POST)

Posting to the record attendance helper endpoint will allow you to create a new attendance and person (or update a person if the system attempts to match people posted with helpers) in one operation. The response is the attendance that was created. While each implementing system will require different fields, any optional fields not included in a post operation should not be set at all by the receiving system, or should be set to default values.

#### Request

```javascript
POST https://osdi-sample-system.org/api/v1/events/c945d6fe-929e-11e3-a2e9-12313d316c29/record_attendance_helper

Header:
OSDI-API-Token:[your api key here]

{
    "person": {
        "identifiers": [
            "foreign_system:1"
        ],
        "family_name": "Edwin",
        "given_name": "Labadie",
        "additional_name": "Marques",
        "origin_system": "OpenSupporter",
        "email_addresses": [
            {
                "address":"test-3@example.com",
                "primary": true,
                "address_type": "Personal"
            }
        ],
        "postal_addresses": [
            {
                "primary": true,
                "address_lines": [
                    "935 Ed Lock"
                ],
                "locality": "New Dudley",
                "region": "MN",
                "postal_code": "17678",
                "country": "RU",
                "address_type": "Home",
                "status": "Verified"
            }
        ],
        "phone_numbers": [
            {
                "primary": true,
                "number": 19876543210,
                "number_type": "Mobile",
                "sms_capable": true
            }
        ],
        "gender": "Male"
    },
    "identifiers": [
        "foreign_system:1"
    ],
    "origin_system": "OpenSupporter",
    "action_date": "2014-03-18T11:02:15Z",
    "status": "accepted",
    "add_tags": [
        "volunteer",
        "donor"
    ],
    "add_lists": [
        "supporters"
    ],
    "triggers": {
        "autoresponse": {
            "enabled": true
        }
    }
}
```

#### Response

```javascript
200 OK

Content-Type: application/hal+json
Cache-Control: max-age=0, private, must-revalidate

{
    "identifiers": [
        "osdi_sample_system:d91b4b2e-ae0e-4cd3-9ed7-de9uemdse",
        "foreign_system:1"
    ],
    "created_date": "2014-03-20T21:04:31Z",
    "modified_date": "2014-03-20T21:04:31Z",
    "action_date": "2014-03-18T11:02:15Z",
    "origin_system": "OpenSupporter",
    "status": "accepted",
    "_links": {
        "self": {
            "href": "https://osdi-sample-system.org/api/v1/events/c945d6fe-929e-11e3-a2e9-12313d316c29/attendances/d91b4b2e-ae0e-4cd3-9ed7-de9uemdse"
        },
        "osdi:event": {
            "href": "https://osdi-sample-system.org/api/v1/events/c945d6fe-929e-11e3-a2e9-12313d316c29"
        },
        "osdi:person": {
            "href": "https://osdi-sample-system.org/api/v1/people/adb951cb-51f9-420e-b7e6-de953195ec86"
        }
    }
}
```

_[Back to top...](#)_

### Scenario: Creating a new attendance and person without authentication (POST)

Posting to the record attendance helper endpoint without authentication will allow you to create a new attendance and person (or update a person if the system attempts to match people posted with helpers) in one operation, but without giving away API key secrets or leaking data, so this method is appropriate for frontend javascript applications. The response is the server resonse code. (ex: 200 for success, 500 for error, etc...) While each implementing system will require different fields, any optional fields not included in a post operation should not be set at all by the receiving system, or should be set to default values.

#### Request

```javascript
POST https://osdi-sample-system.org/api/v1/events/c945d6fe-929e-11e3-a2e9-12313d316c29/record_attendance_helper

{
    "person": {
        "identifiers": [
            "foreign_system:1"
        ],
        "family_name": "Edwin",
        "given_name": "Labadie",
        "additional_name": "Marques",
        "origin_system": "OpenSupporter",
        "email_addresses": [
            {
                "address":"test-3@example.com",
                "primary": true,
                "address_type": "Personal"
            }
        ],
        "postal_addresses": [
            {
                "primary": true,
                "address_lines": [
                    "935 Ed Lock"
                ],
                "locality": "New Dudley",
                "region": "MN",
                "postal_code": "17678",
                "country": "RU",
                "address_type": "Home",
                "status": "Verified"
            }
        ],
        "phone_numbers": [
            {
                "primary": true,
                "number": 19876543210,
                "number_type": "Mobile",
                "sms_capable": true
            }
        ],
        "gender": "Male"
    },
    "identifiers": [
        "foreign_system:1"
    ],
    "origin_system": "OpenSupporter",
    "action_date": "2014-03-18T11:02:15Z",
    "status": "accepted",
    "add_tags": [
        "volunteer",
        "donor"
    ],
    "add_lists": [
        "supporters"
    ],
    "triggers": {
        "autoresponse": {
            "enabled": true
        }
    }
}
```

#### Response

```javascript
200 OK
```

_[Back to top...](#)_