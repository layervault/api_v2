### Revisions

#### Retrieving Revision Information

This call returns information about a given Revision.

**Definition**

    GET /api/v2/revisions/:id

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      'https://api.layervault.com/api/v2/revisions/123'

**Example Response**

```json
{
  "revisions": [{
    "id": "123",
    "href": "https://api.layervault.com/api/v2/revisions/123",
    "slug": "1",
    "revision_number": 1,
    "name": "1",
    "shortened_url": "http://lyrv.lt/abc123",
    "updated_at": "2014-06-12T17:14:22Z",
    "created_at":"2013-06-12T17:14:22Z",
    "deleted_at": null,
    "download_url": "https://layervault.com/files/download_node/abc123",
    "links": {
      "previews": ["101", "102"],
      "user": "1001",
      "metadatum": "123"
    }
  }],
  "linked": {
    // Any linked objects also requested
  }
}
```

**Arguments**

The `:id` supplied to the URL also supports a multi-GET format using a comma-separated string.

To request two revisions with the IDs `123` and `456`, use the following URL:
`https://api.layervault.com/api/v2/revisions/123,456`. The `"revisions"` key in
the JSON response would then contain both revisions.

**Returns**

- HTTP Status: 200 on success.
- HTTP Status: 404 when the revision is not found.
- HTTP Status: 404 when the authenticated user cannot see specified revision.

#### Creating a Revision

Using the API, it's possible to create a revision for a specific file.

**Definition**

    POST /api/v2/revisions

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      -X POST \
      -H "Content-Type: application/vnd.api+json" \
      -d '{ "revisions": [{ ... }] }' \
      'https://api.layervault.com/api/v2/revisions'

**JSON POST Payload**

```json
{
  "revisions": [{
    "md5": "2809857199fe8edd720e7a18935956f8",
    "parent_md5": "ba750b2e87781f35488cd61825d00921",
    "file_data_fingerprint": "d15f367c88d29477c8dec0eb5601afbc",
    "assembled_file_data_fingerprint": "2809857199fe8edd720e7a18935956f8",
    "links": {
      "file": "1001"
    }
  }]
}
```

**Example Response**

The response will be identical to the GET response for a Revision resource.

**Parameters**

- `links/file`: (required, String) The ID of the file to attach this revision to
- `md5`: (optional, String) The expected MD5 of the resulting file
- `parent_md5`: (optional, String) The MD5 of the file that this revision was diffed against
- `file_data_fingerprint`: (optional, String) The MD5 of the partial or complete data blob for the file
- `assembled_file_data_fingerprint`: (option, String) The MD5 of the compelte data blob for the file at this revision

**Notes on the Request**

A `Content-Type` of `application/vnd.api+json` is **required**.

Keep in mind the key you post with is pluralized, i.e. `revisions`.

**Returns**

- HTTP Status: 201 on success.
- HTTP Status: 401 if no user is specified.
- HTTP Status: 403 if the user specified does not have permission to attach a revision to the specified file
- HTTP Status: 404 if the file cannot be found
- HTTP Status: 415 if the `Content-Type` is `application/vnd.api+json`
- HTTP Status: 422 if the POST payload does not conform to the JSON API spec

