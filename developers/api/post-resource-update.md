---
title: Update your resource programmatically
description: You can update your resource programmatically using our API for example, you can update plugin when GH release created
published: true
date: 2021-08-23T20:41:24.180Z
tags: api, resource, update
editor: markdown
dateCreated: 2021-08-23T20:03:26.918Z
---

# Post resource update programmatically

Using our API you can post resource update programmatically

## Example use case:

Post plugin update when GitHub release created or branch merged to master using GH actions. You can build plugin using GH action, and post update to LFM.

## In action

1. [Obtain an access token](/developers/api/access-token) with permissions resource.update.post
2. Make reques to our API
Here's example in bash which should work for any pipeline with curl installed
```bash
curl --location --request POST 'https://api-m.laserflare.net/gql' \
--header 'Authorization: Bearer ${{ secrets.LFM_ACCESS_TOKEN }}' \
--header 'Content-Type: application/json' \
--data-raw '{"query":"mutation publishUpdate ($id: String!, $postId: String, $input: UpdateInput) {\n    publishUpdate (id: $id, postId: $postId, input: $input)\n}","variables":{"id":"${{ secrets.LFM_RESOURCE_ID }}","input":{"version":"<GET FROM pom.xm FOR EXAMPLE>","title":"{\"en\":\"TITLE IN EN\"}"}}}'
```

### Explanation
Code above makes request to our GraphQL API to excecute mutation
```graphql
	mutation publishUpdate ($id: String!, $postId: String, $input: UpdateInput) {
    publishUpdate (id: $id, postId: $postId, input: $input)
}
```
with variables
```json
{
  "id": "${{ secrets.LFM_RESOURCE_ID }}",
  "input": {
    "version": "<GET FROM pom.xm FOR EXAMPLE>,
    "title":"{\"en\":\"TITLE IN EN\"}"
  }
}
```
### Input variables & validation
- version: string, 1 < length < 12, e.g. "1.0.0"
- title: json object as string,
for each language length 5, 50 (optional if description specified)
- subtitle: json object as string,
for each language length 10, 70 (optional)
- description: json object as string,
for each language length 5, 1500 (MD supported, only in view more) (optional if title specified)

Object example // don't forget to stringify!

```json
{
	"en": "New update",
  "es": "Nueva actualización"
}
```

Note: You can post update only in your resource locales (so if your resource locales `["en", "es"]`, you can't post for `"de"`)

An easier way will come soon.