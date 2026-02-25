## In Poseidon
### Step 1
Grab the haulerId of the carrier and use it to find the storeFrontIdV2

### Step 2
Use the storeFrontIdV2 to run Company - Get mutation in insomnia

Use that to grab the user id (ex. VXNlcjoyMDA2NzQwOTM1)

### Step 3
Use the user id in the Staff User Update
```
{
	"id": "VXNlcjoyMDA2NzQwOTM1",
	"input": {
		"firstName": "Marianna",
		"lastName": "Davtyan"
	}
}
```


## In our DB
### Step 1
Use the haulerId to get the uid of the carrier.

### Step 2
Search that in the customer contacts table

