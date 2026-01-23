> If carrier acquisition is in the load notes and the load is booked but showing posted, this should be as simple as setting the load status to ORDERED in the DB

There are times when the load in the AHX site shows a status that differs from the status in Rose Rocket.

###### _Example:_

###### _A load in AHX shows that it is In Transit_

###### _The same load in Rose Rocket shows that it was Delivered_

## Temp Fix

Hit the [https://api.autohaulerexchange.com/rose-rocket-order-event](https://api.autohaulerexchange.com/rose-rocket-order-event) end point

### **Body**

**order_id** (this can be grabbed from URL of the RoseRocket Order) [Required]

**event**

	event: order.in_transit
	
	event: order.delivered
	
	event: order.status_updated (use in tandem with current_status)

**current_status (used in tandem with order.status_updated)**

	current_status: invoice-sent
	
	current_status: invoice-paid

### Examples
![[Pasted image 20260114082655.png]]
![[Pasted image 20260114082812.png]]
![[Pasted image 20260114082830.png]]