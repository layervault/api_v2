### Previews

A Preview is a resource that represents a page, artboard, or slice
for a specific revision.

#### Retrieving Preview Information

**Definition**

    GET /api/v2/previews/:id

**Example Request**


    $ curl -H 'Authorization: Bearer <your access token>' \
      'https://api.layervault.com/api/v2/previews/123'

**Example Response**

```json
{
  "previews": [{
    "id": "123",
    "href": "https://api.layervault.com/api/v2/previews/123",
    "ready": true,
    "page_number": 1,
    "name": "Layer Comp Version A",
    "url": "https://layervault-preview.imgix.net/data/d0ec46c902be6184f2e5d027151fdcd5?fm=png&s=0afeff4bd688cf3385f2de8e6bc67e93",
    "url_140": "https://layervault-preview.imgix.net/data/d0ec46c902be6184f2e5d027151fdcd5?fm=png&s=0afeff4bd688cf3385f2de8e6bc67e93",
    "width": 1024,
    "height": 768,
    "type": "Preview",
    "links": {
      "revision": "1001",
      "feedback_items": ["101", "102"]
    }
  }],
  "linked": {
    // Any linked objects also requested
  }
}
```

**A note about the response**

The `type` attribute will be one of four values: `"Preview"`, `"FailedPreview"`, `"PendingPreview"`, or `"BlankPreview"`

**Arguments**

The `:id` supplied to the URL also supports a multi-GET format using a comma-separated string.

To request two previews with the IDs `123` and `456`, use the following URL:
`https://api.layervault.com/api/v2/previews/123,456`. The `"previews"` key in
the JSON response would then contain both previews.

**Returns**

- HTTP Status: 200 on success.
- HTTP Status: 404 when the preview is not found.
- HTTP Status: 404 when the authenticated user cannot see specified preview.
