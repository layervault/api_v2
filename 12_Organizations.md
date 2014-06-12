### Organizations

#### Retrieving Organization Information

This call returns information about an organization, including any users belonging
to the organization and any projects belonging to the organization.

 Definition

    GET /api/v2/organizations/:id

 Example Request

    $ curl -H 'Authorization: Bearer <your access token>' 'https://api.layervault.com/api/v2/organizations/123'

 Example Response

```json
{
  "organizations": [{
    "id": "123",
    "name": "Acme Corp",
    "slug": "acme-corp",
    "links": {
      "users": ["1001"],
      "administrators": ["1001"],
      "editors": [],
      "spectators": [],
      "projects": ["101"]
    }
  }],
  "linked": {
    "users": [{
      "id": "1001",
      "first_name": "Kelly",
      "last_name": "Sutton"
    }],
    "projects": [{
      "id": "101",
      "name": "iPhone Redesign",
      "slug": "iphone-redesign"
    }]
  }
}
```

#### Arguments

The `:id` supplied to the URL also supports a multi-GET format using a comma-separated string.
To request two organizations with the IDs `123` and `456`, use the following URL:
`https://api.layervault.com/api/v2/organizations/123,456`. The `"organizations"` key in
the JSON response would then contain both organizations.

#### Returns

- HTTP Status: 200 on success.
- HTTP Status: 404 when the organization is not found.
- HTTP Status: 404 when the authenticated user cannot see specified organization.
