# Chef’d Connect API Overview

Chef’d Connect API allows new partners to utilize Chef’d’s platform for meal kit fulfillment. The API can be leveraged to:

* Retrieve [products](#products)
* Retrieve [delivery date](#delivery-dates) options
* [Create](#place-an-order), [track](#track-an-order-get-order-detail), and [cancel](#cancel-an-order) orders
* Retrieve customer [order history](#retrieve-customer-order-history)

The most common use case for Chef'd Connect is the following:

| API User (Merchant)                                                                                             | Customer                      |
| --------------------------------------------------------------------------------------------------------------- | ----------------------------- |
| 1. Retrieve a list of available products from the products endpoint                                             |                               |
|                                                                                                                 | 2. Select product(s) to order |
| 3. Send the selected product(s) and qty(s) to the delivery dates endpoint<br> to retrieve delivery date options |                               |
|                                                                                                                 | 4. Select a delivery date     |
|                                                                                                                 | 5. Checkout                   |
| 6. Place an order for the desired product(s) and qty(s) to be delivered<br> on the specified date               |                               |
| 7. Listen for shipping updates provided via webhooks and provide them<br> to the customer                       |                               |
|                                                                                                                 | 8. Cook like a pro!           |

## Base URL

All URLs referenced in the documentation have the following base:

|               Staging                |          Production          |
| :----------------------------------: | :--------------------------: |
| https://connect-staging.chefd.com/v2 | https://connect.chefd.com/v2 |

## API Authentication

Requests to the API are made using secure app tokens. Your token information along with your merchant-id must be provided in the request header in order to authenticate. The token includes:

    app-id
    app-secret
    merchant-id

Tokens can be generated and updated by logging into the merchant portal (see below).

# Getting Started

## Merchant Portal and Gaining API Access

This portion of the documentation assumes you have already signed an agreement with Chef’d and are ready for account setup. If you have not done so, please contact Chef'd to start that process.

After you have been confirmed as a Chef’d Connect merchant, you will be asked to choose an email address and password. Using these credentials, log into the merchant portal at the following address:

|                   Staging                    |              Production              |
| :------------------------------------------: | :----------------------------------: |
| https://connect-staging.chefd.com/auth/login | https://connect.chefd.com/auth/login |

Once logged in, you will need to do the following:

1. Create a Shop.
   * Click the Shops navigation link.
   * Fill out the required fields and click Create when finished.
2. Create an App token.
   * Click the Apps navigation link.
   * Click Create App to generate an App token.

You may now use the generated token credentials to begin interacting with the API.

## (Optional) Set up Stripe Integration

While setting up your account, you should have been given the option to handle billing via either invoice or Stripe API integration. The following only applies if you chose the latter option.

Chef’d Connect utilizes [Stripe](https://stripe.com/)’s secure payment platform for all payment processing. To set this feature up, follow these steps:

1. Navigate to the Dashboard page of the Merchant Portal and click the button labeled “Connect with Stripe”. You will be redirected to Stripe’s website.
2. If you have a Stripe account, enter the account credentials. Otherwise follow the prompts to create a Stripe account.
3. Once this has been completed you will be redirected to the Merchant Portal’s dashboard and your Stripe account will be connected and ready for transactions.

To find out more about how Chef'd Connect uses Stripe, see the detailed [Stripe section](#stripe-integration-detail) toward the bottom of this document.

# Using the API

All responses to **successful** requests will adhere to the following format:

```javascript
{
  "message_id": "msg101",
  "success": true,
  "data": {
    ...
  }
}
```

All responses to **failed** requests will adhere to the following format:

```javascript
{
    "message_id": "msg101",
    "success": false,
    "errors": [
        {
            "message": "Ooops, something went wrong to authorize your request!",
            "param": ""
        }
    ]
}
```

Every request must include a valid `merchant-id`, `app-id`, and `app-secret` in the request header. The sample requests below assume this information was provided.

For initial API exploration and testing, most merchants have found it easiest to use a [Postman](https://www.getpostman.com/) collection. Chef'd can provide a basic Postman collection and environments upon request.

## **Products**

### **List all products**

Returns a list of all available products. The products are returned in order, sorted alphabetically.

_Definition_

`GET /products`

_Arguments_

| Argument | Description                                                                                       | Required |
| -------- | ------------------------------------------------------------------------------------------------- | :------: |
| `limit`  | A limit on the number of objects to be returned.                                                  |          |
| `offset` | A cursor for use in pagination.                                                                   |          |
| `sort`   | A field to sort the results by (A list of valid options will be provided soon).                   |          |
| `filter` | Field/value pairs to filter the results by.                                                       |          |
| `search` | A search term to limit the results by. Only products containing the search term will be returned. |          |
| `fields` | A list of fields to limit the response body to.                                                   |          |

_Sample Request_

```javascript
GET https://connect.chefd.com/v2/products
```

_Sample Response_

```javascript
{
  "message_id": "msg101",
  "success": true,
  "data": {
    "has_more": true,
    "items": [
      {
        "product_sku": "BEEF1",
        "title": "Beef Bourguignon",
        "subtitle": "with Mashed Potatoes and Herbed Green Beans",
        "description":
          "Beef Bourguignon with Mashed Potatoes and Herbed Green Beans",
        "detailed_description":
          "Beef tenderloin elevates this classic comfort-food. This lean cut of meat cooks more quickly than classic stew meat and still delivers everything we love about Beef Bourguignon. You will create a rich sauce studded with golden mushrooms and pearl onions, enhanced with plenty of red wine and just enough smoky bacon. A sauce this good deserves a bed of creamy mashed potatoes and some crisp green beans seasoned with lots of fresh, woodsy thyme. Bon appétit!",
        "partner_id": 128357,
        "partner_name": "Chef'd",
        "type_of_meal": "Dinner",
        "cuisine": "French",
        "calories_per_serving": 260,
        "cook_time_in_minutes": 50,
        "skill_level": "Medium",
        "spice_level": "None",
        "proteins": "Beef",
        "image": "https://img.chefd.com/images/image_files/BEEF1.2%20Hero.jpg",
        "images": [
          "https://img.chefd.com/images/image_files/BEEF1.2%20Action2.jpg",
          "https://img.chefd.com/images/image_files/BEEF1.2%20Action.jpg",
          "https://img.chefd.com/images/image_files/BEEF1.2%20Hero.jpg",
          "https://img.chefd.com/images/image_files/BEEF1.2%20Label.jpg"
        ],
        "nutritional_facts":
          "https://img.chefd.com/images/image_files/BEEF1.2%20Label.jpg",
        "variants": [
          {
            "sku": "BEEF1.2",
            "variant_title": "Dinner for 2",
            "inventory_quantity": 264,
            "num_of_servings": 2,
            "retail_price": "29.00"
          },
          {
            "sku": "BEEF1.4",
            "variant_title": "Dinner for 4",
            "inventory_quantity": 132,
            "num_of_servings": 4,
            "retail_price": "44.00"
          }
        ],
        "allergens": ["Milk", "Wheat"],
        "suggested_pairings": ["Burgundy", "Stout"],
        "ingredients": [
          "Beef Stock Concentrate",
          "Beef Tenderloin Cubes",
          "Whipping Cream",
          "Butter",
          "All Purpose Flour",
          "Pearl Onions",
          "Tomato Paste",
          "Red Cooking Wine",
          "Bacon",
          "Green Beans",
          "Garlic Clove",
          "Chives",
          "Fresh Thyme",
          "Crimini Mushrooms",
          "Russet Potato"
        ],
        "equipments": [
          "Colander",
          "Large 3-inch Deep Sauté Pan",
          "Medium Bowl",
          "Medium Sauce Pot with Lid",
          "Potato Masher"
        ],
        "from_your_pantry": ["Black Pepper", "Olive Oil", "Salt"],
        "recipe_category": ["Family Friendly", "Gourmet"],
        "reccommendation_protien_filters": [],
        "dietary_lifestyle": []
      },
      {
        ...
      }
    ]
  }
}
```

## **Delivery Dates**

### **Get available delivery dates**

Returns a list of available delivery dates based on the order contents. Each date is serialized and expires after a limited time to ensure accuracy. The dates are returned in order, sorted by soonest date.

_Definition_

`GET /dates`

_Arguments_

| Argument         | Description                                                                                | Required |
| ---------------- | ------------------------------------------------------------------------------------------ | :------: |
| `zipcode`        | The destination zipcode for the order. Must be in 5 or 9 digit format.                     |    ✓     |
| `is_residential` | Indicates the shipping address type. Boolean.                                              |    ✓     |
| `products`       | Represents the order contents. A JSON array of line item objects. Can be one or more skus. |    ✓     |

_Sample Request_

```javascript
GET https://connect.chefd.com/v2/dates/?zipcode=90245&is_residential=true&products=[{"sku":"BEEF1.2","qty":1}]
```

_Sample Response_

```javascript
{
  "message_id": "msg101",
  "success": true,
  "data": {
    "zipcode": "90245",
    "is_residential": true,
    "skus": ["BEEF1.2"],
    "delivery_dates": [
      {
        "id": "OXOhhbdw",
        "date": "2018-01-23T20:00:00-08:00",
        "valid_until": "2018-01-19T16:55:00-08:00",
        "transit_time": 1,
        "location": "elsegundo"
      },
      {
        "id": "OpUxeslc",
        "date": "2018-01-24T20:00:00-08:00",
        "valid_until": "2018-01-19T16:55:00-08:00",
        "transit_time": 1,
        "location": "elsegundo"
      },
      {
        "id": "lrHVJXjG",
        "date": "2018-01-25T20:00:00-08:00",
        "valid_until": "2018-01-19T16:55:00-08:00",
        "transit_time": 1,
        "location": "elsegundo"
      },
      {
        "id": "aJIKZ8DO",
        "date": "2018-01-26T20:00:00-08:00",
        "valid_until": "2018-01-19T16:55:00-08:00",
        "transit_time": 1,
        "location": "elsegundo"
      },
      {
        ...
      }
    ]
  }
}
```

## **Orders**

### **Place an order**

To place an order, you must have first retrieved a valid delivery date. The body of an order is comprised of the following:

* customer information
* line items
* shipping address
* delivery date
* (optional) payment related fields - note below that all payment related fields are required if a merchant has chosen to utilize Chef'd Connect for billing via Stripe

_Definition_

`POST /orders`

_Arguments_

| Argument            | Description                                                                                    | Required |
| ------------------- | ---------------------------------------------------------------------------------------------- | :------: |
| `merchant_order_id` | An alphanumeric string used by the merchant to identify the order in the merchant's system.    |    ✓     |
| `total_price`       | The total cost of all line items plus shipping.                                                |          |
| `subtotal_price`    | The total cost of all line items before shipping is added.                                     |          |
| `total_tax`         | The sum of all applicable tax.                                                                 |          |
| `total_discounts`   | The sum of any applied discounts.                                                              |          |
| `shipping_price`    | The cost of shipping.                                                                          |          |
| `currency`          | The currency code (currently, only `USD` is supported).                                        |          |
| `created_at`        | The date the order is being placed.                                                            |    ✓     |
| `customer`          | An object representing the customer this order is being placed for.                            |    ✓     |
| `customer.address`  | An object representing the billing address for the customer.                                   |    ✓     |
| `line_items`        | An array of objects containing `sku`, `quantity`, and `price`.                                 |    ✓     |
| `shipping_address`  | An object representing the destination shipping address. Can be the same as `customer.address` |    ✓     |
| `delivery_details`  | The delivery date object retrieved from the `/dates` endpoint.                                 |    ✓     |
| `payment_details`   | An object containing a valid `stripe_customer_id` or `stripe_token`.                           |          |
| `notify_customer`   | Indicates whether the customer would like to receive order updates.                            |    ✓     |

_Sample Request_

```javascript
POST https://connect.chefd.com/v2/orders

{
  "merchant_order_id": "23kj4h4cslv",
  "total_price": "49",
  "subtotal_price": "39",
  "total_tax": "0",
  "total_discounts": "0",
  "shipping_price": "10",
  "currency": "usd",
  "created_at": "2018-01-19T04:23:00-08:00",
  "customer": {
    "id": "fkhj234kj52",
    "email": "jdoe@gmail.com",
    "first_name": "John",
    "last_name": "Doe",
    "phone": "+1-555-555-5555",
    "address": {
      "id": "14ykly3xh539",
      "company": "",
      "address1": "291 Coral Circle",
      "address2": "",
      "city": "El Segundo",
      "zipcode": "90245",
      "state_code": "CA",
      "country_code": "US"
    }
  },
  "line_items": [
    {
      "sku": "BEEF1.2",
      "quantity": 1,
      "price": "39"
    }
  ],
  "shipping_address": {
    "id": "14ykly3xh539",
    "company": "",
    "address1": "291 Coral Circle",
    "address2": "",
    "city": "El Segundo",
    "zipcode": "90245",
    "state_code": "CA",
    "country_code": "US",
    "first_name": "John",
    "last_name": "Doe",
    "phone": "+1-555-555-5555",
    "email": "jdoe@gmail.com"
  },
  "delivery_details": {
    "id": "J7gbUubu",
    "delivery_date": "2018-01-23T20:00:00-08:00"
  },
  "payment_details": {
    "stripe_token": "tok_amex"
  },
  "notify_customer": true
}
```

_Sample Response_

```javascript
{
  "message_id": "msg101",
  "success": true,
  "data": {
    "supplier_order_id": "DE-KNR42",
    "merchant_order_id": "23kj4h4cslv",
    "ordered_date": "2018-01-03T04:23:00-08:00",
    "total_price": "49.00",
    "subtotal_price": "39.00",
    "total_tax": "0.00",
    "total_discounts": "0.00",
    "shipping_price": "10.00",
    "currency": "usd",
    "customer": {
      "id": "fkhj234kj52",
      "email": "jdoe@gmail.com",
      "first_name": "John",
      "last_name": "Doe",
      "phone": "+1-555-555-5555",
      "address": {
        "id": "14ykly3xh539",
        "company": "",
        "address1": "291 Coral Circle",
        "address2": "",
        "city": "El Segundo",
        "zipcode": "90245",
        "state_code": "CA",
        "country_code": "US"
      }
    },
    "line_items": [
      {
        "sku": "BEEF1.2",
        "quantity": 1,
        "price": "39"
      }
    ],
    "shipping_address": {
      "id": "14ykly3xh539",
      "company": "",
      "address1": "291 Coral Circle",
      "address2": "",
      "city": "El Segundo",
      "zipcode": "90245",
      "state_code": "CA",
      "country_code": "US",
      "first_name": "John",
      "last_name": "Doe",
      "phone": "+1-555-555-5555",
      "email": "jdoe@gmail.com"
    },
    "delivery_details": {
      "id": "J7gbUubu",
      "delivery_date": "2018-01-23T20:00:00-08:00"
    },
    "payment_details": {
      "stripe_customer_id": "cus_3bse83nfksaf4bsj"
    },
    "notify_customer": true,
    "message": "Order Placed successfully",
    "status": true
  }
}
```

### **Track an order / Get order detail**

Retrieves the details of an existing order, including tracking information. You must provide the `supplier_order_id` that was returned when the order was placed.

_Definition_

`GET /orders/?supplier_order_id={id}`

_Arguments_

| Argument            | Description                                                | Required |
| ------------------- | ---------------------------------------------------------- | :------: |
| `supplier_order_id` | The unique id that was returned when the order was placed. |    ✓     |

_Sample Request_

```javascript
GET https://connect.chefd.com/v2/orders/?supplier_order_id=DE-KNR42
```

_Sample Response_

```javascript
{
  "message_id": "msg101",
  "success": true,
  "data": {
    "supplier_order_id": "DE-KNR42",
    "merchant_order_id": "23kj4h4cslv",
    "ordered_date": "2018-01-03T04:23:00-08:00",
    "total_price": "49.00",
    "subtotal_price": "39.00",
    "total_tax": "0.00",
    "total_discounts": "0.00",
    "shipping_price": "10.00",
    "currency": "usd",
    "customer": {
      "id": "fkhj234kj52",
      "email": "jdoe@gmail.com",
      "first_name": "John",
      "last_name": "Doe",
      "phone": "+1-555-555-5555",
      "address": {
        "id": "14ykly3xh539",
        "company": "",
        "address1": "291 Coral Circle",
        "address2": "",
        "city": "El Segundo",
        "zipcode": "90245",
        "state_code": "CA",
        "country_code": "US"
      }
    },
    "line_items": [
      {
        "sku": "BEEF1.2",
        "quantity": 1,
        "price": "39"
      }
    ],
    "shipping_address": {
      "id": "14ykly3xh539",
      "company": "",
      "address1": "291 Coral Circle",
      "address2": "",
      "city": "El Segundo",
      "zipcode": "90245",
      "state_code": "CA",
      "country_code": "US",
      "first_name": "John",
      "last_name": "Doe",
      "phone": "+1-555-555-5555",
      "email": "jdoe@gmail.com"
    },
    "delivery_details": {
      "delivery_date": "2018-01-23T20:00:00-08:00",
      "ship_date": "2018-01-22T04:00:00-08:00",
      "ship_method": "FedEx Ground"
    },
    "tracking_details": {
      "carrier": "FedEx",
      "carrier_url":
        "https://track.easypost.com/djE6dHJrX2M4OTIwZjI5YWEyMjRlMzg5YjVhOTU2NWNkNzY2M2Y0",
      "tracking_code": "424410081508",
      "status": "out_for_delivery",
      "actual_delivery_date": "2018-01-23T20:00:00-08:00",
      "estimated_delivery_date": "2018-01-23T06:00:00+00:00",
      "signed_by": null,
      "events": [
        {
          "message": "Shipment information sent to FedEx",
          "status": "pre_transit",
          "date": "2018-01-22T17:08:00+00:00",
          "source": "FedEx",
          "tracking_location": {
            "city": "CARSON",
            "state": "CA",
            "zipcode": null,
            "country": null
          }
        },
        {
          "message": "Picked up",
          "status": "in_transit",
          "date": "2018-01-23T01:19:00+00:00",
          "source": "FedEx",
          "tracking_location": {
            "city": "CARSON",
            "state": "CA",
            "zipcode": null,
            "country": null
          }
        },
        {
          "message": "Arrived at FedEx location",
          "status": "in_transit",
          "date": "2018-01-23T04:44:00+00:00",
          "source": "FedEx",
          "tracking_location": {
            "city": "CARSON",
            "state": "CA",
            "zipcode": null,
            "country": null
          }
        },
        {
          "message": "At local FedEx facility",
          "status": "in_transit",
          "date": "2018-01-23T12:06:00+00:00",
          "source": "FedEx",
          "tracking_location": {
            "city": "CARSON",
            "state": "CA",
            "zipcode": null,
            "country": null
          }
        },
        {
          "message":
            "On FedEx vehicle for delivery (Scheduled for delivery next business day)",
          "status": "out_for_delivery",
          "date": "2018-01-23T12:12:00+00:00",
          "source": "FedEx",
          "tracking_location": {
            "city": "EL SEGUNDO",
            "state": "CA",
            "zipcode": null,
            "country": null
          }
        }
      ]
    },
    "cancellable": false
  }
}
```

### **Cancel an order**

Cancels an order. This operation may only be performed within a limited window of time after the order was placed. If the order has started to be fulfilled, the order will not be able to be cancelled. As a general rule, orders within 2 days of their intended delivery date are likely not cancellable. You can always call the [order detail](#track-an-order-get-order-detail) endpoint to ensure that `"cancellable": true` for the order you would like to cancel.

_Definition_

`DELETE /products/?supplier_order_id={id}`

_Arguments_

| Argument            | Description                                   | Required |
| ------------------- | --------------------------------------------- | :------: |
| `supplier_order_id` | The id of the order you would like to cancel. |    ✓     |

_Sample Request_

```javascript
DELETE https://connect.chefd.com/v2/orders/?supplier_order_id=DE-KNR42
```

_Sample Success Response_

```javascript
{
    "message_id": "msg101",
    "success": true,
    "data": "Order 'DE-KNR42' cancelled successfully!"
}
```

_Sample Failure Response_

```javascript
{
    "message_id": "msg101",
    "success": false,
    "errors": [
        {
            "message": "Order is not cancellable!",
            "param": ""
        }
    ]
}
```

### **Retrieve customer order history**

Returns a list of all orders belonging to a single customer.

_Definition_

`GET /orders/?customer_id={id}`

_Arguments_

| Argument      | Description                                                     | Required |
| ------------- | --------------------------------------------------------------- | :------: |
| `customer_id` | The id of the customer whose orders you would like to retrieve. |    ✓     |

_Sample Request_

```javascript
GET https://connect.chefd.com/v2/orders/?customer_id=fkhj234kj52
```

_Sample Response_

```javascript
{
  "message_id": "msg101",
  "success": true,
  "data": {
    "has_more": true,
    "items": [
      {
        "supplier_order_id": "DE-KNR42",
        "merchant_order_id": "23kj4h4cslv",
        "ordered_date": "2018-01-03T04:23:00-08:00",
        "total_price": "49.00",
        "subtotal_price": "39.00",
        "total_tax": "0.00",
        "total_discounts": "0.00",
        "shipping_price": "10.00",
        "currency": "usd",
        "customer": {
          "id": "fkhj234kj52",
          "email": "jdoe@gmail.com",
          "first_name": "John",
          "last_name": "Doe",
          "phone": "+1-555-555-5555",
          "address": {
            "id": "14ykly3xh539",
            "company": "",
            "address1": "291 Coral Circle",
            "address2": "",
            "city": "El Segundo",
            "zipcode": "90245",
            "state_code": "CA",
            "country_code": "US"
          }
        },
        "line_items": [
          {
            "sku": "BEEF1.2",
            "quantity": 1,
            "price": "39"
          }
        ],
        "shipping_address": {
          "id": "14ykly3xh539",
          "company": "",
          "address1": "291 Coral Circle",
          "address2": "",
          "city": "El Segundo",
          "zipcode": "90245",
          "state_code": "CA",
          "country_code": "US",
          "first_name": "John",
          "last_name": "Doe",
          "phone": "+1-555-555-5555",
          "email": "jdoe@gmail.com"
        },
        "delivery_details": {
          "delivery_date": "2018-01-23T20:00:00-08:00",
          "ship_date": "2018-01-22T04:00:00-08:00",
          "ship_method": "FedEx Ground"
        },
        "tracking_details": {
          "carrier": "FedEx",
          "carrier_url":
            "https://track.easypost.com/djE6dHJrX2M4OTIwZjI5YWEyMjRlMzg5YjVhOTU2NWNkNzY2M2Y0",
          "tracking_code": "424410081508",
          "status": "out_for_delivery",
          "actual_delivery_date": "2018-01-23T20:00:00-08:00",
          "estimated_delivery_date": "2018-01-23T06:00:00+00:00",
          "signed_by": null,
          "events": [
            {
              "message": "Shipment information sent to FedEx",
              "status": "pre_transit",
              "date": "2018-01-22T17:08:00+00:00",
              "source": "FedEx",
              "tracking_location": {
                "city": "CARSON",
                "state": "CA",
                "zipcode": null,
                "country": null
              }
            },
            {
              "message": "Picked up",
              "status": "in_transit",
              "date": "2018-01-23T01:19:00+00:00",
              "source": "FedEx",
              "tracking_location": {
                "city": "CARSON",
                "state": "CA",
                "zipcode": null,
                "country": null
              }
            },
            {
                ...
            }
          ]
        },
        "cancellable": false
      },
      {
          ...
      }
    ]
  }
}
```

# **Using Shipping Webhooks**

Chef'd Connect leverages [Easypost API](https://www.easypost.com/) to provide real time shipping updates to merchants.

## **Setup**

To register a webhook handler:

1. Sign into your merchant portal.
2. Click the "Shops" link.
3. In the "Webhook Url" field, enter the target URL to which Chef'd Connect should send the webhooks.

Chef'd Connect will send a POST request to the listed endpoint each time the order's shipping status changes.

## **Authentication**

Chef’d Connect will provide a header parameter called `sign` as a means for the merchant to authenticate the incoming message before processing it. This is an optional security step.

The `sign` parameter is generated by hashing the merchant app-secret and the posted data object.

## **Responding to Webhooks**

To acknowledge receipt of a webhook, a merchant's webhook handler should return a 2xx HTTP status code. All response codes outside this range will indicate to Chef'd Connect that you did not receive the webhook. Currently, Chef'd Connect does not retry the webhook event. This will likely be added in a future release.

## **Shipping Update Simulator - _STAGING ONLY_**

Chef'd Connect offers a shipping update simulator to allow merchants to test their webhook handlers. When a merchant places an order, the simulator will generate an Easypost tracker for one of the following seven event types:

| Event              | Tracking Number |
| ------------------ | --------------- |
| `pre-transit`      | EZ1000000001    |
| `in_transit`       | EZ2000000002    |
| `out_for_delivery` | EZ3000000003    |
| `delivered`        | EZ4000000004    |
| `return_to_sender` | EZ5000000005    |
| `failure`          | EZ6000000006    |
| `unknown`          | EZ7000000007    |

The randomly selected update event will also include any prior events. For example, if the simulator generates a `delivered` event, the shipping update will also contain the `pre_transit`, `in_transit`, and `out_for_delivery` events.

## **Sample Webhook POST Request**

The following is an example of what Chef'd Connect will send to a merchant's webhook handler. Note that the object is identified as an order and that it maintains the exact same format as the order object returned from the orders endpoint.

```javascript
{
  "object": "order",
  "data": {
    "supplier_order_id": "DE-KNR42",
    "merchant_order_id": "23kj4h4cslv",
    "ordered_date": "2018-01-03T04:23:00-08:00",
    "total_price": "49.00",
    "subtotal_price": "39.00",
    "total_tax": "0.00",
    "total_discounts": "0.00",
    "shipping_price": "10.00",
    "currency": "usd",
    "customer": {
      "id": "fkhj234kj52",
      "email": "jdoe@gmail.com",
      "first_name": "John",
      "last_name": "Doe",
      "phone": "+1-555-555-5555",
      "address": {
        "id": "14ykly3xh539",
        "company": "",
        "address1": "291 Coral Circle",
        "address2": "",
        "city": "El Segundo",
        "zipcode": "90245",
        "state_code": "CA",
        "country_code": "US"
      }
    },
    "line_items": [
      {
        "sku": "BEEF1.2",
        "quantity": 1,
        "price": "39"
      }
    ],
    "shipping_address": {
      "id": "14ykly3xh539",
      "company": "",
      "address1": "291 Coral Circle",
      "address2": "",
      "city": "El Segundo",
      "zipcode": "90245",
      "state_code": "CA",
      "country_code": "US",
      "first_name": "John",
      "last_name": "Doe",
      "phone": "+1-555-555-5555",
      "email": "jdoe@gmail.com"
    },
    "delivery_details": {
      "delivery_date": "2018-01-23T20:00:00-08:00",
      "ship_date": "2018-01-22T04:00:00-08:00",
      "ship_method": "FedEx Ground"
    },
    "tracking_details": {
      "carrier": "FedEx",
      "carrier_url":
        "https://track.easypost.com/djE6dHJrXzRhYTY4ZGFjOGNjNzRlZDNhMTExZjhiMzZmMmU0NDRj",
      "tracking_code": "EZ1000000001",
      "status": "pre_transit",
      "actual_delivery_date": "2018-01-23T20:00:00-08:00",
      "estimated_delivery_date": "2018-01-23T06:00:00+00:00",
      "signed_by": null,
      "events": [
        {
          "message": "Pre-Shipment information received",
          "status": "pre_transit",
          "date": "2018-01-21T16:45:41+00:00",
          "source": "EasyPost",
          "tracking_location": {
            "city": null,
            "state": null,
            "zipcode": null,
            "country": null
          }
        },
        {
          "message": "Shipping label created",
          "status": "pre_transit",
          "date": "2018-01-21T11:34:41+00:00",
          "source": "EasyPost",
          "tracking_location": {
            "city": null,
            "state": null,
            "zipcode": null,
            "country": null
          }
        }
      ]
    },
    "cancellable": false
  }
}
```

# **Stripe Integration Detail**

Merchants that do not have full e-commerce capabilities can let Chef'd Connect handle the processing of customer payments.

Chef'd Connect operates as a Platform account in order to be able to process charges created by another party (the merchant). The merchant operates as a Connected account - accepting customer payment information, submitting it to Stripe to create a charge, then passing a secure token to Chef'd Connect for billing. More details on Stripe's API can be found by reading their [API docs](https://stripe.com/docs/connect).

The following explains the basic steps involved in charging a customer with Stripe.

## **Charging a _new_ customer**

1. Customer enters billing and credit card information into merchant UI and clicks to purchase.
2. Merchant uses Stripe API to create a charge token.
3. Merchant sends order to Chef'd Connect, including the charge token.
4. Chef'd Connect creates a new Stripe customer object by sending customer billing information and token to Stripe.
5. Chef'd Connect submits the charge to Stripe as a [destination charge](https://stripe.com/docs/connect/destination-charges).
6. Chef'd Connect provides a response to the merchant indicating whether the charge was accepted or rejected. This response includes the `customer_id`.
7. Merchant saves `customer_id` to their database.
8. Merchant renders response to the customer.

## **Charging an _existing_ customer**

1. Customer logs into merchant website.
2. Merchant retrieves `customer_id` of logged in customer.
3. Customer clicks to purchase using saved credit card and billing information.
4. Merchant sends order to Chef'd Connect, including the `customer_id`.
5. Chef'd Connect submits the charge to Stripe as a [destination charge](https://stripe.com/docs/connect/destination-charges).
6. Chef'd Connect provides a response to the merchant indicating whether the charge was accepted or rejected. This response includes the `customer_id`.
7. Merchant renders response to the customer.

# **Release Log**

## v2.0.0 - release date 9/18/17 (released 8:14am PT 9/18/17)

* supplier_id is no longer the key for the product – it is now product_sku
* supplier_id is no longer the key for the product variant – it is now sku
* merchant-id is no longer part of the query string – it is now required in the header
* fields specific to variants are no longer in the product data. Specifically:
  * price
  * num_of_servings
  * variant_title
  * inventory_quantity
* fields specific to products are no longer in the variant data. Specifically:
  * title
  * subtitle
  * type_of_meal
* placing an order requires the use of the variant sku instead of the supplier_id
* response codes will be 4xx for failures in validation instead of 2xx
* all json responses will include these four sections:
  * message_id (optional)
  * success
  * errors
  * data
* validation error responses will include error details and the name(s) of the failing field(s)
* all dates will be provded in responses in iso format
* we will continue to take the unix date format as input to accomodate merchants live on previous version but we will move to all iso dates in next release
* price is not provided by the products endpoint because pricing is determined by the merchant
* pricing information is provided by the merchant as each merchant has a unique pricing policy
* pricing information is only used by Chef'd if Chef'd is handling billing, otherwise this data is just for convenience purposes if merchant would like to access in the future
* shipping updates implemented

## v2.0.1 - release date 9/25/17 (released 7:36am PT 9/25/17)

* all integer dates will be replaced with iso 8601 for both input and output
* all fields ending in \_iso will be removed
* Stripe requirements finalized
* Stripe design finalized

## v2.0.2 - release date 10/2/17 (released 7:02am PT 10/2/17)

* shipping tracker simulator - staging will provide shipping updates to merchant provided url utilizing a process that will mimic the data provided by shippers
* testing - positive and negative tests integrated into Jenkins for products and dates endpoint

## v2.0.3 - release date 10/9/17 (released 6:42am PT 10/9/17)

* security - require merchant-id, app-id and app-secret in the dates endpoint
* testing - positive and negative tests integrated into Jenkins for orders endpoint
* testing - performance tests integrated into Jenkins for products, dates and orders endpoints
* production - ready for a production test to ship a box

## v2.0.4 - release date 10/16/17 (released 5:32am PT 10/16/17)

* orders - zip code basic validation added for billing address
* orders - state basic validation added for billing address
* logging - verbose logging enabled to investigate performance issues with products and dates endpoints
* testing - end to end tests created for placing orders and all related endpoints

## v2.0.5 - release date 10/30/17 (released 4:50am PT 10/31/17)

* Stripe - Stripe order processing completed for submitting charges
* products - performance issue resolved with sub 500ms response time
* products - product skus updated to differentiate from variant sku

## v2.0.6 - release date 11/06/17 (released 6:04am PT 11/07/17)

* dates - performance issues first iteration of improvements (average 1 second response time)

## v2.0.7 - release date 11/13/17 (released 7:30am PT 11/14/17)

* dates - performance issues second iteration of improvements
* Stripe - save customer information completed for connected account
* orders - bug order not getting to netsuite fixed

## v2.0.8 - release date 11/20/17 (released 8:12am PT 11/20/17)

* orders - cancel order endpoint first release
* Stripe - create merchant and submit order complete for connected account

## v2.0.9 - release date 12/4/17 (released 7:10am PT 12/4/17)

* orders - cancel order endpoint updates
* orders - added 'cancellable' field to GET to check cancel status
* products - retail_price added to the products endpoint (for reference)

## v2.1.0 - release date 1/15/18 (released 6:20am PT 1/15/18)

* orders - discount field enabled
* shipping updates - fixed shipping updates load balancer issue
* orders - made 'email' field associated with customer

## Proposed future features

* API - Add shipping address location validation - 5 days
* API - Implement retries on shipment updates - 5 days
* API - Accept discount codes - 10 days
* API - Nutritional Information - 8 days
* Portal - Make it easier to select the products in portal - 3 days
* Portal - need to indicate which fields are required when creating a shop
* Portal - Shop URL field required to begin with http://
* Portal - Add description of fields (i.e. webhook and what it is used for)
* Apps - need to be able to delete an app
* Apps - clean up the UI of the app page, formatting is broken unless page is extremely large
* Apps - change button label of the circling arrows to read “refresh app secret” or something similar
* Apps - add “delete app” button near “refresh app secret” button

## Bugs Reported

* 9/25/17 - When order is placed validation of available quantity is not working (status: complete)
* 9/29/17 - Improve response time on dates endpoint (status: improved to 1 second so far)
* 10/4/17 - Response time for products is slow when there are several products (status: complete)
* 10/6/17 - No basic validation for shipping zip code (statas: complete)
* 10/6/17 - No basic validation for shipping state (status: complete)
* 10/6/17 - No basic validation for shipping town (status: complete)
* 10/13/17 - Zip code of 9 digits not supported (status: backlog)
* 11/10/17 - Order failed to get to Netsuite (status: complete)
* 12/20/17 - shipping updates failing (status: complete)
* 1/4/18 - Email confirmation not working (status: found issue to be fixed 1/19/18)
