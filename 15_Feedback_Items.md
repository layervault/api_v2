### Feedback Items

#### Retrieving Feedback Item Information

This call returns information about a given item of feedback, along with
replies and user information.

**Definition**

    GET /api/v2/feedback_items/:id

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      'https://api.layervault.com/api/v2/feedback_items/123'

**Example Response**

```json
{
  "feedback_items": [{
    "id": "123",
    "href": "https://api.layervault.com/api/v2/feedback_items/123",
    "message": "Looking good!",
    "top": 0,
    "right": 100,
    "bottom": 100,
    "left": 0,
    "links": {
      "user": "101",
      "preview": "1001",
      "feedback_items": ["4001", "4002"] // Reply items
    }
  }],
  "linked": {
    "previews": [
      // Preview objects
    ],
    "users": [
      // User objects
    ],
    "feedback_items": [
      // Associate Feedback Items, such as replies
    ]
  }
}
```

**Arguments**

The `:id` supplied to the URL also supports a multi-GET format using a comma-separated string.
To request two organizations with the IDs `123` and `456`, use the following URL:
`https://api.layervault.com/api/v2/organizations/123,456`. The `"organizations"` key in
the JSON response would then contain both organizations.

**Notes about the Response**

The `top`, `right`, `bottom`, and `left` properties will be set to non-null
values if an annotation was included with this feedback item. These are relative
pixel values to the top-left corner of the parent preview, as defined by the
`links/preview` property.

**Returns**

- HTTP Status: 200 on success.
- HTTP Status: 404 when the organization is not found.
- HTTP Status: 404 when the authenticated user cannot see specified organization.

#### Creating a Feedback Item

Creating a Feedback Item adheres to the [JSON API spec for creating resources](http://jsonapi.org/format/#updating-creating-a-document).

There are two types of feedback items: new threads and new replies. New threads
are feedback items attached to Preview objects, whereas new replies are attached to
other feedback items.

**Definition**

    POST /api/v2/feedback_items

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      -X POST \
      -H "Content-Type: application/vnd.api+json" \
      -d '{ "feedback_items": [{ ... }] }' \
      'https://api.layervault.com/api/v2/feedback_items'

**JSON POST Payload**

```json
{
  "feedback_items": [{
    "message": "Loving the direction!",
    "top": 0,
    "bottom": 100,
    "left": 0,
    "right": 100,
    "links": {
      "preview": "1001"
    }
  }]
}
```

**Example Response**

```json
{
  "feedback_items": [{
    "id": "123",
    "href": "https://api.layervault.com/api/v2/feedback_items/123",
    "message": "Loving the direction!",
    "top": 0,
    "right": 100,
    "bottom": 100,
    "left": 0,
    "links": {
      "user": "101",
      "preview": "1001"
    }
  }],
  "linked": {
    "previews": [
      // Preview objects
    ],
    "users": [
      // User objects
    ],
    "feedback_items": [
      // Associate Feedback Items, such as replies
    ]
  }
}
```

**Parameters**

- `message`: (required, String) Message of the feedback item
- `links/preview`: (required, String) ID of the preview to leave the feedback on
- `links/feedback_item`: (optional, String) ID of the feeback item to reply to
- `top`: (optional, Integer) Pixel top of the annotation
- `left`: (optional, Integer) Pixel left of the annotation
- `bottom`: (optional, Integer) Pixel bottom of the annotation
- `right`: (optional, Integer) Pixel right of the annotation

**Notes on the Request**

A `Content-Type` of `application/vnd.api+json` is **required**.

Keep in mind the key you post with is pluralized, i.e. `feedback_items`.

**Returns**

- HTTP Status: 201 on success.
- HTTP Status: 401 if no user is specified.
- HTTP Status: 403 if the user specified does not have permission leave feedback on ths given page
- HTTP Status: 404 if one of the resources (parent feedback item or preview) cannot be found
- HTTP Status: 415 if the `Content-Type` is `application/vnd.api+json`
- HTTP Status: 422 if the POST payload does not conform to the JSON API spec

#### Updating a Feedback Item

You can update a feedback item's message after it has been published like so.

Only the user that published the feedback item can also edit it.

**Definition**

    PATCH /api/v2/feedback_items/:id

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      -X PATCH \
      -H "Content-Type: application/json-patch+json" \
      -d '[ ... ]' \
      'https://api.layervault.com/api/v2/feedback_items/123'

**JSON PATCH Payload**

```json
[
  { "op": "replace", "path": "/feedback_items/0/message", "value": "Actually, I changed my mind. I don't like this." }
]
```

**Example Response**

The endpoint will respond with the updated feedback item resource in the same format as the GET request.

**Editable Properties**

- `message`: (String) Message of the feedback item

**Returns**

- HTTP Status: 201 on success.
- HTTP Status: 400 if the PATCH payload is not valid JSON
- HTTP Status: 403 if the user specified cannot edit the feedback item
- HTTP Status: 404 if the feedback item cannot be found
- HTTP Status: 415 if the `Content-Type` is `application/json-patch+json`
- HTTP Status: 422 if the PATCH payload indicated any operation other than `"replace"`

#### Deleting a Feedback Item

You can delete a feedback item if your user ran their mouth too much.

Only the user that published the feedback item or an organization's administrator can
delete the feedback item. All others will be greeted with an `HTTP 403`.

**Definition**

    DELETE /api/v2/feedback_items/:id

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      -X DELETE \
      'https://api.layervault.com/api/v2/feedback_items/123'

**Example Response**

The response's body will be empty

**Returns**

- HTTP Status: 204 on success.
- HTTP Status: 401 if no user is authenticated
- HTTP Status: 403 if the user specified cannot delete the the feedback item
- HTTP Status: 404 if the feedback item cannot be found

