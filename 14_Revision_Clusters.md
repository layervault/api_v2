### Revision Clusters

Revision Clusters are grouped revisions that belong to a file resource. They are
used primarily by the end-user to organize a file's history. Using the API,
it is possible to create and destroy these revision clusters.

Revision clusters are not required to attach revision to files; it is possible to have
revisions that belong to file without belonging to a cluster.

#### Retrieving Revision Cluster Information

This call returns information for a given revision cluster, along with any revisions
attached to it via the `linked/revisions` property.

**Definition**

    GET /api/v2/revision_clusters/:id

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      'https://api.layervault.com/api/v2/revision_clusters/123'

**Example Response**

```json
{
  "revision_clusters": [{
    "id": "123",
    "href": "https://api.layervault.com/api/v2/revision_clusters/123",
    "links": {
      "file": "1001",
      "revisions": ["4001", "4002", "4003"]
    }
  }],
  "linked": {
    "revisions": [
      // Revision objects
    ],
    "files": [
      // File objects
    ]
  }
}
```

**Arguments**

The `:id` supplied to the URL also supports a multi-GET format using a comma-separated string.
To request two revision clusters with the IDs `123` and `456`, use the following URL:
`https://api.layervault.com/api/v2/revision_clusters/123,456`. The `"revision_clusters"` key in
the JSON response would then contain both revision clusters.

**Returns**

- HTTP Status: 200 on success.
- HTTP Status: 404 when the revision cluster is not found.
- HTTP Status: 404 when the authenticated user cannot see specified revision cluster.

#### Creating a Revision Cluster

Creating a revision cluster is done by grouping 1 or more revisions in a request.

**Definition**

    POST /api/v2/revision_clusters

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      -X POST \
      -H "Content-Type: application/vnd.api+json" \
      -d '{ "revision_clusters": [{ ... }] }' \
      'https://api.layervault.com/api/v2/revision_clusters'

**JSON POST Payload**

```json
{
  "revision_clusters": [{
    "links": {
      "revisions": ["4001", "4002", "4003"]
    }
  }]
}
```

**Example Response**

```json
{
  "revision_clusters": [{
    "id": "123",
    "href": "https://api.layervault.com/api/v2/revision_clusters/123",
    "links": {
      "file": "1001",
      "revisions": ["4001", "4002", "4003"]
    }
  }],
  "linked": {
    "revisions": [
      // Revision objects
    ],
    "files": [
      // File objects
    ]
  }
}
```

**Parameters**

- `links/revisions`: (required, Array of Strings) IDs for the revisions to add to the new cluster

**Notes on the Request**

A `Content-Type` of `application/vnd.api+json` is **required**.

Keep in mind the key you post with is pluralized, i.e. `revision_clusters`.

**Returns**

- HTTP Status: 201 on success.
- HTTP Status: 400 if all revisions do not belong to the same file
- HTTP Status: 400 if one or more of the revisions belongs to an existing revision cluster
- HTTP Status: 401 if no user is specified.
- HTTP Status: 403 if the user specified does not have permission modify the given revisions
- HTTP Status: 404 if one of revisions cannot be found
- HTTP Status: 415 if the `Content-Type` is `application/vnd.api+json`
- HTTP Status: 422 if the POST payload does not conform to the JSON API spec

#### Deleting a Revision Cluster

You can delete a revision cluster just as easily as you created one. If a revision cluster
exists after the revision cluster to be deleted, all revisions will be added to the proceeding
revision cluster.

**Definition**

    DELETE /api/v2/revision_clusters/123

**Example Request**

    $ curl -H 'Authorization: Bearer <your access token>' \
      -X DELETE \
      'https://api.layervault.com/api/v2/revision_clusters/123'

**Example Response**

The response's body will be empty

**Returns**

- HTTP Status: 204 on success.
- HTTP Status: 400 if more than one revision cluster is specified
- HTTP Status: 401 if no user is authenticated
- HTTP Status: 403 if the user specified cannot delete the the revision cluster
- HTTP Status: 404 if the revision cluster cannot be found

