### Files

#### Retrieving File Information

This call returns information about a file, including all associated
revision clusters, revisions, previews, feedback items, and users. It's a
doozy.

**Definition**

    GET /api/v2/files/:id

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      'https://api.layervault.com/api/v2/files/123'

**Example Response**

```json
{
  "files": [{
    "id": "123",
    "name": "My File.psd",
    "slug": "my-file--psd",
    "local_path": "~/LayerVault/Project/My File.psd",
    "updated_at": "2014-06-12T17:14:22Z",
    "created_at":"2013-06-12T17:14:22Z",
    "deleted_at": null,
    "download_permission": false,
    "view_permission": "anyone",
    "can_edit_node": true,
    "can_comment_on_file": true,
    "links": {
      "revision_clusters": ["1", "2", "3"],
      "folder": "5001"
    }
  }],
  "linked": {
    "revision_clusters": [
      // Revision cluster objects
    ],
    "revisions": [
      // Revision cluster objects
    ],
    "previews": [
      // Revision cluster objects
    ],
    "feedback_items": [
      // Revision cluster objects
    ],
    "users": [
      // Revision cluster objects
    ]
  }
}
```

#### Arguments

The `:id` supplied to the URL also supports a multi-GET format using a comma-separated string.
To request two files with the IDs `123` and `456`, use the following URL:
`https://api.layervault.com/api/v2/files/123,456`. The `"files"` key in
the JSON response would then contain both files.

#### Notes about the Response

The respones will contain two properties scoped to the current user: `can_edit_node` and
`can_comment_on_file`. These can and will change if the authenticated user changes.

#### Returns

- HTTP Status: 200 on success.
- HTTP Status: 404 when the file is not found.
- HTTP Status: 404 when the authenticated user cannot see specified file.

#### Updating a File

Updating a file is useful when you would like to change the `view_permission` or the
`download_permission` properties. You can also change the `name` property.

All update requests must include valid [JSON Patch (RFC 6902) documents](http://tools.ietf.org/html/rfc6902).

**Definition**

    PATCH /api/v2/files/123

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      -X PATCH \
      -H "Content-Type: application/json-patch+json" \
      -d '[ ... ]' \
      'https://api.layervault.com/api/v2/files/123'

**JSON PATCH Payload**

```json
[
  { "op" => "replace", "path" => "files/0/view_permission", "value" => "disabled" }
]
```

**Example Response**

This endpoint will respond with the update file resource in the same format as the GET request.

**Editable Properties**

- `updated_at`: (Date) The timestamp when the file was last changed
- `view_permission`: (String) Ability of others to see the revision links for this file. Possible values: `"anyone"`, `"members"`, `"disabled"`
- `download_permission`: (Boolean) Toggles the ability to download files from revision link pages
- `name`: (String) The name of a file. Must end in a file extension.

**Returns**

- HTTP Status: 201 on success.
- HTTP Status: 400 if the PATCH payload is not valid JSON
- HTTP Status: 403 if the user specified cannot edit the file
- HTTP Status: 404 if the file cannot be found
- HTTP Status: 415 if the `Content-Type` is `application/json-patch+json`
- HTTP Status: 422 if the PATCH payload indicated any operation other than `"replace"`