# Getting Started with Monetary Engage API
---
## Objective
Monetary's Engage platform objective is to simplify implementation of marketing functionality for POS developers, and streamline/simplify marketing for Merchants.


## 1, 2, 3, MARKETING!

In the walkthrough below, we will cover how to authorize an API call to the Engage platform, register a customer for loyalty, and submit that customer's first receipt data that could spawn offers!

### 1. Common Headers

Every request requires two headers:
#### Authorization
is easy, just insert your secret key in the Authorization header:

Authorization: `secretKEYGOESHERE`

#### Content Types
The Engage only utilizes Content-Type: `application/json`

### 2. Register Customer for Loyalty

To register a customer for loyalty, utilize a `POST` to the [v1/Customers](https://engage-cert.monetary.co/swagger/ui/index#!/Customers/Customers_Post) endpoint.

#### Example Customer Registration Request:
```
{
  "CustomerID": 123,
  "ExternalID": "GGBilbo",
  "Phone": "5551231234",
  "Email": "bbaggens@fictionalEmail.com",
  "FirstName": "Bob",
  "LastName": "Baggens",
  "Address": "123 Fake Street",
  "City": "Durango",
  "State": "CO",
  "Zipcode": "81303",
  "Birthday": "2017-04-20",
  "AcceptsSMS": true,
  "AcceptsEmail": true
 }
 ```
 ### 3. Submit a Receipt
 
 To make complex marketing decisions based upon customer buying patterns, we just need the basic receipt data. `PSOT` this receipt data to the [v1/Receipts](https://engage-cert.monetary.co/swagger/ui/index#!/Receipts/Receipts_Post) endpoint.
 
 #### Example Receipt Request:
 ```
 {
  "CustomerID": 123,
  "Skus": [
    {
      "SKU": "SOCKS",
      "Description": "AWESOME 80s Socks!",
      "Amount": 10.50,
      "Quantity": 2
    }
  ],
  "Discounts": [],
  "SubTotal": 21.00,
  "Taxes": 1.58,
  "Total": 22.58
}
 ```
 #### Example Response
 ```
 {
  "ReceiptID": "0383b1c2438e4f82b516543de3522a4b",
  "CustomerID": 123,
  "Skus": [
    {
      "SKU": "SOCKS",
      "Description": "AWESOME 80s Socks!",
      "Amount": 10.50,
      "Quantity": 2
    }
  ],
  "Discounts": [],
  "SubTotal": 21,
  "Taxes": 0.075,
  "Tip": 0,
  "Shipping": 0,
  "Total": 22.58
}
 ```
 ### Marketing!
 
 If you have integrated these points, you have provided enough information Monetary to provide highly detailed, adaptive marketing functionality. For more information skip ahead to the [appendix](#apendx) to see more actions our API can take, such as finding existing Customers, reviewing generated offers, etcetera!
 
 ### (Advanced) Campaign Creation
 If your Point of Sale already has marketing and loyalty type functionality, but your customer base would like to send your offers (coupons, discounts, free item codes, etc) via the Monetary Notification Platform, you can configure a Monetary Campaign. Monetary campaigns will submit an offer via SMS, Email, and/or App directly to the registered customer!
 
 <a id="apendex"></a>
 ## Appendix
 
 Below are additional resources for other endpoints (look up a customer for future purchases, etc.)
 
 ### Customer Specific Actions:
 * [Search Registered Customers](https://engage-cert.monetary.co/swagger/ui/index#!/Customers/Customers_Get)
 * [Register Customer](https://engage-cert.monetary.co/swagger/ui/index#!/Customers/Customers_Post)
 * [Retrieve Single Customer](https://engage-cert.monetary.co/swagger/ui/index#!/Customers/Customers_Get_0)
 * [Update a Registered Customer](https://engage-cert.monetary.co/swagger/ui/index#!/Customers/Customers_Put)
 * [Retrieve Customer Offers](https://engage-cert.monetary.co/swagger/ui/index#!/Customers/Customers_GetOffers)
 * [Retrieve Customer Receipt Data that has been submitted](https://engage-cert.monetary.co/swagger/ui/index#!/Customers/Customers_GetReceipts)
 
 ### Offer Specific Actions
 * [Get Offer by Code](https://engage-cert.monetary.co/swagger/ui/index#!/Offers/Offers_Get)
 * [Get Offer by id](https://engage-cert.monetary.co/swagger/ui/index#!/Offers/Offers_Get_0)
 
 ### Receipt Specific Actions
 * [Search Receipt Data](https://engage-cert.monetary.co/swagger/ui/index#!/Receipts/Receipts_Get)
 * [Submit Receipt Data](https://engage-cert.monetary.co/swagger/ui/index#!/Receipts/Receipts_Post)
 * [Get Receipt by ReceiptID](https://engage-cert.monetary.co/swagger/ui/index#!/Receipts/Receipts_Get_0)
 
 ## (Advanced) Campaign Specific Actions
 If your point of sale has its own marketing strategy, but you would like to utilize the same notification and offers Monetary's automated campaigns generate,  you can also utilize the monetary Campaign endpoint. These endpoints create manual campaigns. Manual campaigns are ways to deliver offers (coupons, discounts, etc.) to registered customers via POST to the Campaigns/Offers. Follow these easy steps to create and send offers to registered customers:
 
 ### 1. Create a Manual Campaign
 Submit a payload describing the offer you would like to send to registered customers. There are two endpoints that take and respond with the same payload:
 ##### Campaigns/Location
 Post here if you want an offer to only be used at a single location in a franchise/marketing group. It also Limits offers to only registered customers who have made a purchase at the location specified by the API key.
 ##### Campaigns/Group
 Post here if you want an offer to be available/redeemable at any location in the franchise/marketing group. Any registered customer can receive and redeem an offer generated with this campaign. Only the holding business is authorized to create group level campaigns.
 
 #### Example Request
 ```
 {
  "AmountOff": 10.05,
  "MinimumPurchase": 0.5,
  "DaysTillExpiration": 31
}
 ```
 #### Example Response
 ```
 {
  "CampaignID": 1,
  "TemplateName": "manual",
  "AmountOff": 10.05,
  "MinimumPurchase": 0.5,
  "DaysTillExpiration": 31,
  "IsLocationSpecific": true
}
 ```
 ### 2. Submit Customer List to Generate Offers
 once you have the CampaignID, you can utilize the `Campaigns/{CampaignID}/Offers` endpoint to submit a list of CustomerID identifiers for the registered customers who should receive the offer:
 #### Example Request
 ```
 [101,345,332]
 ```
 #### Example Response
 `204` Request accepted (no content)
 
 Once we receive the list of customers, we determine if the list of customers can receive an offer for the given CampaignID (i.e. if the campaign is location specific, and if the customers in the list have made a purchase at the given location). 
