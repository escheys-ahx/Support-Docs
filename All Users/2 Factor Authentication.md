In the rare case that someone needs to have their 2fa deactivated...

SCHEMA
```
mutation MetadataUpdate($id: ID!, $input: [MetadataInput!]!) {
  updateMetadata(id: $id, input: $input) {
    errors {
      code
      field
      message
    }
    item {
      metadata {
        key
        value
      }
    }
  }
}
```

BODY
```
{
	"id": "VXNlcjoyMDA2NzQxOTE2",
	"input": [
		{
			"key": "TwoFactorOverride",
			"value": "true"
		}
	]
}
```

You can get `id` from the Staffers Mutation
SCHEMA
```
query GetUsers($id: [ID!], $search: String) {
	staffUsers(filter: { search: $search, ids: $id }, first: 100) {
		edges {
			node {
				id
				updatedAt
				dateJoined
				
				defaultCompany {
					id
					name
				}
				defaultBillingAddress {
					id
					firstName
					lastName
					isDefaultBillingAddress
					companyName
					streetAddress1
					streetAddress2
					city
					countryArea
					country {
						code
						country
					}
					postalCode
					phone
				}
				permissionGroups {
					id
					name
				}
				avatar {
					alt
					url
				}
				firstName
				lastName
				email
				languageCode
				isStaff
				isActive
				addresses {
					id
				}
				companies {
					id
					name
				}
				metadata {
					key
					value
				}
				privateMetadata {
					key
					value
				}
				addresses {
					id
					firstName
					lastName
					isDefaultBillingAddress
					companyName
					streetAddress1
					streetAddress2
					city
					countryArea
					country {
						code
						country
					}
					postalCode
					phone
				}
			}
		}
	}
}

```

BODY
```
{
	"search": "AAlarid+Infinity@santafemazda.com"
}
```
Where you search with the users email.