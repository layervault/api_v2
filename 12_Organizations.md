## Methods

### Organizations

#### Retrieving Organization Information

This call returns information about an organization, including any users belonging
to the organization and any projects belonging to the organization.

 Definition

    GET /api/v2/organizations/:id

 Example Request

    $ curl -H 'Authorization: Bearer <your access token>' \
      'https://api.layervault.com/api/v2/organizations/123'

 Example Response

```json
{
  "organizations": [{
    "id": "123",
    "name": "Acme Corp",
    "slug": "acme-corp",
    "href": "https://api.layervault.com/api/v2/organizations/123",
    "is_free": false,
    "deleted_at": null,
    "updated_at": "2014-06-12T17:14:22Z",
    "created_at":"2013-06-12T17:14:22Z",
    "created_at": "",
    "sync_type": "layervault",
    "cancelled_at": null,
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
  },
  "links": {
    "organizations.projects": {
      "href": "https://api.layervault.com/api/v2/organizations/{organizations.projects}",
      "type": "projects"
    },
    "organizations.users": {
      "href": "https://api.layervault.com/api/v2/organizations/{organizations.users}",
      "type": "users"
    },
    "organizations.administrators": {
      "href": "https://api.layervault.com/api/v2/organizations/{organizations.administrators}",
      "type": "users"
    },
    "organizations.editors": {
      "href": "https://api.layervault.com/api/v2/organizations/{organizations.editors}",
      "type": "users"
    },
    "organizations.spectators": {
      "href": "https://api.layervault.com/api/v2/organizations/{organizations.spectators}",
      "type": "users"
    }
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
