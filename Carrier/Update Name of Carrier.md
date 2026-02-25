### Step 1
Grab the uid of the carrier and use it to find the storeFrontIdV2

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


