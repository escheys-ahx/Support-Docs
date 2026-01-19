This is an issue that occasionally comes up when a shipper tries to post a load.


The condition that needs to be passed for them to get this error is...

`!sellerContant || (!storefrontID && !storefrontIDV2`

In the most recent case that I found, the shipper was logging in with the email associated to their account, which is correct.

It however did not correlate to any of their users emails.


To fix this we propose to change on of the user emails to the company email.
