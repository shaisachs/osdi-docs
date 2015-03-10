---
layout: default
title: Record Canvass Helper
---

# Record Canvass Helper

This document defines the Record Canvass Helper resource. 

The Record Attendance Helper is a helper endpoint to aid in the creation of [Canvass](canvasses.html), [Answer](answers.html), and [Tagging](taggingss.html) resources via POST. It provides a quick and easy way to create a canvass as well as all the data collected during that canvass, eliminating the need for multiple POST operations to store that information.

The Record Canvass Helper assumes that the person who was canvassed (the "target") already exists in the system.

The response to a Record Canvass Helper POST is the full representation of the Canvass.

Some initial implementations may only support helpers -- direct RESTful access may not be supported. In those cases, the _links section may be omitted in responses.


### Sections:

* [Endpoints and URL structures](#endpoints-and-url-structures)
* [Fields](#fields)
	* [Common Fields](#common-fields)
    * [Record Canvass Helper Fields](#record-canvass-helper-fields)
    * [Related Objects](#related-objects)
* [Related Resources](#related-resources)
* [Scenarios](#scenarios)
    * [Scenario: Creating a new canvass (POST)](#scenario-creating-a-new-canvass-post)


{% include endpoints_and_url_structures.md %}

The link relation label for the Record Canvass Helper is ```osdi:record_canvass_helper```.

_[Back to top...](#)_


## Fields

{% include fields_intro.md %}

{% include global_fields_helper.md %}

_[Back to top...](#)_


### Record Canvass Helper Fields

A list of fields specific for POSTing via the Record Canvass Helper.

| Name          | Type      | Description
|-----------    |-----------|-----------|--------------
|origin_system		|string     |A human readable identifier of the system where this attendance was created. (ex: "OSDI System")
|action_date		|string		|The date and time the canvass was attempted.
| contact_type  | string | A code indicating the method by which the person was contacted.  For example: "in-person"
| input_type    | string | A code indicating the method by which the canvass is being input into the system. For example: "mobile"
| result_code  | string | A code indicating the status of the contact attempt.  For example: "not-home" indicates that the contact failed because the target was not home, while "success" indicates that the target was contacted successfully.  An empty or missing value for result_code should be assumed to mean that the contact was successful.

_[Back to top...](#)_


### Related Objects

These JSON hashes included in the table above are broken out into their own tables for readability, rather than independent resources with their own endpoints.

#### Person

|Name          |Type      |Description
|-----------    |-----------|--------------
|answer      |[Answer*](answers.html)     |An inlined hash representation of an answer, containing any valid fields for the Answer resource.
|tagging      |[Tagging*](taggings.html)     |An inlined hash representation of an answer, containing any valid fields for the Answer resource.

_[Back to top...](#)_


## Related Resources

* [Answer](answers.html)
* [Tagging](taggings.html)

_[Back to top...](#)_


## Scenarios

{% include scenarios_helper_intro.md %}


### Scenario: Creating a new canvass (POST)

Posting to the record canvass helper endpoint will allow you to create a new canvass and the associated answers and/or taggings in one operation. The response is the attendance that was created. While each implementing system will require different fields, any optional fields not included in a post operation should not be set at all by the receiving system, or should be set to default values.

#### Request

```javascript
POST https://osdi-sample-system.org/api/v1/people/c945d6fe-929e-11e3-a2e9-12313d316c29/record_canvass_helper

Header:
OSDI-API-Token:[your api key here]

{
    "canvass": {
      "origin_system": "OSDI Sample System",
      "action_date": "2014-03-18T11:02:15Z",
      "contact_type": "in-person",
      "input_type": "mobile",
      "result_code": ""
    },
    "answers": [
      {
        "identifiers": [
          "osdi_sample_system:d91b4b2e-ae0e-4cd3-9ed7-d0ec501b0bc3",
          "foreign_system:1"
        ],
        "value": "He's not sure",
        "_links": {
          "osdi:question": {
            "href": "https://osdi-sample-system.org/api/v1/questions/c945d6fe-929e-11e3-a2e9-12313d316c29"
          }
        }
      },
      {
        "identifiers": [
          "osdi_sample_system:d91b4b2e-ae0e-4cd3-9ed7-d0ec501b0bc4",
          "foreign_system:1"
        ],
        "origin_system": "OSDI Sample System",
        "responses": [
          "Y"
        ],
        "_links": {
          "osdi:question": {
            "href": "https://osdi-sample-system.org/api/v1/questions/c945d6fe-929e-11e3-a2e9-12313d316c33"
          }
        }
      }
    ],
    "taggings": [
      {
        "identifiers": [
          "osdi_sample_system:1efc3644-af25-4253-90b8-a0baf12dbd1e"
        ],
        "_links": {
          "osdi:tag": {
            "href": "https://osdi-sample-system.org/api/v1/tags/c945d6fe-929e-11e3-a2e9-12313d316c29"
          }
        }
      }
    ]
}
```

#### Response

```javascript
200 OK

Content-Type: application/hal+json
Cache-Control: max-age=0, private, must-revalidate

{
    "identifiers": [
        "osdi_sample_system:d91b4b2e-ae0e-4cd3-9ed7-d0ec501b0bc3",
        "foreign_system:1"
    ],
    "origin_system": "OSDI Sample System",
    "created_date": "2014-03-20T21:04:31Z",
    "modified_date": "2014-03-20T21:04:31Z",
    "action_date": "2014-03-18T11:02:15Z",
    "contact_type": "in-person",
    "input_type": "mobile",
    "result_code": "",
    "_links": {
        "self": {
            "href": "https://osdi-sample-system.org/api/v1/people/c945d6fe-929e-11e3-a2e9-12313d316c29/canvasses/d91b4b2e-ae0e-4cd3-9ed7-d0ec501b0bc3"
        },
        "osdi:target": {
            "href": "https://osdi-sample-system.org/api/v1/people/c945d6fe-929e-11e3-a2e9-12313d316c29"
        },
        "osdi:answers": {
            "href": "https://osdi-sample-system.org/api/v1/people/c945d6fe-929e-11e3-a2e9-12313d316c29/canvasses/d91b4b2e-ae0e-4cd3-9ed7-d0ec501b0bc3/answers"
        },
        "osdi:taggings": {
            "href": "https://osdi-sample-system.org/api/v1/people/c945d6fe-929e-11e3-a2e9-12313d316c29/canvasses/d91b4b2e-ae0e-4cd3-9ed7-d0ec501b0bc3/taggings"
        }
    }
}
```


_[Back to top...](#)_