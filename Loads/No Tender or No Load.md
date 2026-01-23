# Remediation

- Refactoring Poseidon GraphQL calls to use a centralized utility with better error handling and connection pooling, which improves reliability and performance.

##  No Tender #, not in RR

If a load was booked, but no tender # was generated

##  Solution

1. Get the order number (eg. 84151)

2. Navigate to RoseRocket and search the orders for that order number, there should be no result

3. Grab Load ID from the url of the load page on the site

    1. `SELECT * FROM loads WHERE loadId = "{loadId}";`

	2. Grab the loadShortCode

4. Find the order-retry endpoint and paste the loadShortCode into the body, then run it.

5. On success, you should check that Order has RoseRocket ID and that it appears in RR also


---

##  No Tender #, but in RR

There is no tender #, but the order exists in Rose Rocket

>WARNING: This is only to be done if it is still in pending status. Otherwise you can link the external id and the tenderID in the load metadata. Make sure load short code is correct, then in load meta data set the TenderID.

##  Solution

1. Find the loadShortCode in the database, using the loadId from the url in AHX

2. Change the short code to {shortCode}-1

3. Locate the order-retry endpoint that uses the loadShortCode, and paste that new shortCode into the endpoint. Then run it

4. Go back into Rose Rocket, they should now have tags, then refresh in AHX site and it should have the tender

5. Go back to Rose Rocket, click the old tender, hit cancel, then delete it.


##  No Tender# and No Load
##  Solution

1. The bandaid solution to this is that the order needs to be cancelled and rebooked, then order-retry steps if needed.

