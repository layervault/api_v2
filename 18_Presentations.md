### Presentations

Presentations are a great way to show off the finished product of your
work. You can create new presentations or add existing previews to
presentations via the API.

#### Retrieving Presentation Information

This call returns information for a given presentation. Additional resources
can be requested via the `include` parameter.

**Definition**

    GET /api/v2/presentations/:id

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      'https://api.layervault.com/api/v2/presentations/123'

**Example Response**

```json
{
  "presentations": {
    "id": "123",
    "href": "https://api.layervault.com/api/v2/presentations/123",
    "links": {
      "project": "1001"
    }
  },
  "linked": {
    "projects": [
      // Project resources, if requested
    ]
  }
}
```

**Arguments**

The `:id` supplied to the URL also supports a multi-GET format using a comma-separated string.
To request two presentations with the IDs `123` and `456`, use the following URL:
`https://api.layervault.com/api/v2/presentations/123,456`. The `"presentations"` key in
the JSON response would then contain both presentations.

**Returns**

- HTTP Status: 200 on success
- HTTP Status: 404 when the presentation is not found
- HTTP Status: 404 when the authenticated user cannot see the specified presentation

#### Creating a Presentation

Creating a presentation is achieved by attached a presentation to a project resource
with zero or more presentation items.

**Definition**

    POST /api/v2/presentations

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      -X POST \
      -H "Content-Type: application/vnd.api+json" \
      -d '{ "presentations": [{ ... }] }' \
      'https://api.layervault.com/api/v2/presentations'

**JSON POST Payload**

```json
{
  "presentations": [{
    "links": {
      "project": "1001",
      "previews": ["1", "2", "3"]
    }
  }]
}
```

**Example Response**

See the response generated from the GET request for a presentation.

**Parameters**

- `links/project` (required, String) ID of the Project to attach this presentation to
- `links/previews` (optional, Array of Strings) Zero or more IDs of previews to add to this presentation

**Notes on the Request**

A `Content-Type` of `application/vnd.api+json` is **required**.

Keep in mind that the key you POST with is pluralized, i.e. `presentations`.

**Returns**

- HTTP Status: 201 on success.
- HTTP Status: 400 if all previews are not in the same project
- HTTP Status: 401 if no user is specified.
- HTTP Status: 403 if the user specified does not have permission modify the given previews
- HTTP Status: 403 if the user cannot modify the specified project
- HTTP Status: 404 if one of previews cannot be found
- HTTP Status: 415 if the `Content-Type` is `application/vnd.api+json`
- HTTP Status: 422 if the POST payload does not conform to the JSON API spec

#### Adding a preview to a Presentation

Revisions can be added to a presentation at any time. If a preview has already been added to
the presentation, it will be added again.

**Definition**

    PATCH /api/v2/presentations/:id

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      -X PATCH \
      -H "Content-Type: application/json-patch+json" \
      -d '[ ... ]' \
      'https://api.layervault.com/api/v2/presentations/123'

**JSON PATCH Payload**

```json
[
  { "op": "add", "path": "/presentations/0/links/previews/-", "value": "456" }
]
```

Note: `456` is the existing preview's ID.

**Example Response**

The endpoint will respond with the updated presentation resource in the same format as the GET request.

**Editable Properties**

- `links/previews` (String) The ID of a preview you wish to add to the specified presentation

**Returns**

- HTTP Status: 200 on success.
- HTTP Status: 400 if the PATCH payload is not valid JSON
- HTTP Status: 403 if the user specified cannot edit the presentation
- HTTP Status: 404 if the presentation cannot be found
- HTTP Status: 415 if the `Content-Type` is `application/json-patch+json`
- HTTP Status: 422 if the PATCH payload indicated any operation other than `"add"`
