# Chef’d Connect API Overview

Chef’d Connect API allows new partners to utilize Chef’d’s platform for meal kit fulfillment. The API can be leveraged to:

* Retrieve products
* Retrieve delivery date options
* Create, track, and cancel orders
* Retrieve customer order history

A common use case for Chef'd Connect is the following:

| API User (Merchant)                                                                                         | Customer                      |
| ----------------------------------------------------------------------------------------------------------- | ----------------------------- |
| 1. Retrieve a list of available products from the products endpoint                                         |                               |
|                                                                                                             | 2. Select product(s) to order |
| 3. Send the selected product(s) and qty(s) to the delivery dates endpoint to retrieve delivery date options |                               |
|                                                                                                             | 4. Select a delivery date     |
|                                                                                                             | 5. Checkout                   |
| 6. Place an order for the desired product(s) and qty(s) to be delivered on the specified date               |                               |
| 7. Listen for shipping updates provided via webhooks and provide them to the customer                       |                               |
|                                                                                                             | 8. Cook like a pro!           |

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

This portion of the documentation assumes you have already signed an agreement with Chef’d and are ready for account setup. If you have not done so, please contact <someemailaddress> to start that process.

After you have been confirmed as a Chef’d Connect merchant, you will be asked to choose an email address and password. Using these credentials, log into the merchant portal at the following address:

|                   Staging                    |              Production              |
| :------------------------------------------: | :----------------------------------: |
| https://connect-staging.chefd.com/auth/login | https://connect.chefd.com/auth/login |

Once logged in, you will need to do the following:

1. Create a Shop
   * Click the Shops navigation link
   * Fill out the required fields and click Create when finished
2. Create an App token
   * Click the Apps navigation link
   * Click Create App to generate an App token.

You may now use the generated token credentials to begin interacting with the API.

## (Optional) Set up Stripe Integration

While setting up your account, you should have been given the option to handle billing via either invoice or Stripe API integration. The following only applies if you chose the latter option.

Chef’d Connect utilizes Stripe’s secure payment platform for all payment processing. To set this feature up, follow these steps:

1. Navigate to the Dashboard page of the Merchant Portal and click the button labeled “Connect with Stripe”. You will be redirected to Stripe’s website.
2. If you have a Stripe account, enter the account credentials. Other wise follow the prompts to create a Stripe account.
3. Once this has been completed, you will be redirected to the Merchant Portal’s dashboard and your Stripe account will be connected and ready for transactions.

# Using the API

All responses to successful requests will adhere to the following format:

```javascript
{
  "message_id": "msg101",
  "success": true,
  "data": {
    ...
  }
}
```

All responses to failed requests will adhere to the following format:

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
| limit    | A limit on the number of objects to be returned.                                                  |          |
| offset   | A cursor for use in pagination.                                                                   |          |
| sort     | A field to sort the results by (A list of valid options will be provided soon).                   |          |
| filter   | Field/value pairs to filter the results by.                                                       |          |
| search   | A search term to limit the results by. Only products containing the search term will be returned. |          |
| fields   | A list of fields to limit the response body to.                                                   |          |

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

| Argument         | Description                                                                                |        Required         |
| ---------------- | ------------------------------------------------------------------------------------------ | :---------------------: |
| `zipcode`        | The destination zipcode for the order. Must be in 5 or 9 digit format.                     | :ballot_box_with_check: |
| `is_residential` | Indicates the shipping address type. Boolean.                                              | :ballot_box_with_check: |
| `products`       | Represents the order contents. A JSON array of line item objects. Can be one or more skus. | :ballot_box_with_check: |

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

To place an order, you must have first retrieved a valid delivery date. The body of an order

_Definition_

`POST /orders`

_Arguments_

| Argument            | Description                                                                                 |        Required         |
| ------------------- | ------------------------------------------------------------------------------------------- | :---------------------: |
| `merchant_order_id` | An alphanumeric string used by the merchant to identify the order in the merchant's system. | :ballot_box_with_check: |
| `total_price`       | The total cost of all line items plus shipping.                                             |                         |
| `subtotal_price`    | The total cost of all line items before shipping is added.                                  |                         |
| `total_tax`         | The sum of all applicable tax.                                                              | :ballot_box_with_check: |
| `total_discounts`   | The sum of any applied discounts.                                                           | :ballot_box_with_check: |
| `shipping_price`    | The cost of shipping.                                                                       | :ballot_box_with_check: |
| `currency`          | The currency code (currently, only `USD` is supported).                                     | :ballot_box_with_check: |
| `created_at`        | The date the order is being placed.                                                         | :ballot_box_with_check: |
| `customer`          | An object representing the customer this order is being placed for.                         | :ballot_box_with_check: |
| `line_items`        | An array of objects containing `sku`, `quantity`, and `price`.                              | :ballot_box_with_check: |
| `shipping_address`  | An object representing the destination shipping address.                                    | :ballot_box_with_check: |
| `delivery_details`  | The delivery date object retrieved from the `/dates` endpoint.                              | :ballot_box_with_check: |
| `payment_details`   | An object containing a valid `stripe_customer_id` or `stripe_token`.                        |                         |
| `notify_customer`   | Indicates whether the customer would like to receive order updates.                         | :ballot_box_with_check: |

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

### **Track an order**

Retrieves the details of an existing order. You must supply the `supplier_order_id` returned when the order was placed.

_Definition_

`GET /orders/?supplier_order_id={id}`

_Arguments_

| Argument          | Description                                                |        Required         |
| ----------------- | ---------------------------------------------------------- | :---------------------: |
| supplier_order_id | The unique id that was returned when the order was placed. | :ballot_box_with_check: |

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

Cancels an order. This operation may only be performed within a limited window of time after the order was placed. If the order has started to be fulfilled, the order will not be able to be cancelled.

_Definition_

`DELETE /products/?supplier_order_id={id}`

_Arguments_

| Argument          | Description                                   |        Required         |
| ----------------- | --------------------------------------------- | :---------------------: |
| supplier_order_id | The id of the order you would like to cancel. | :ballot_box_with_check: |

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

| Argument    | Description                                                     |        Required         |
| ----------- | --------------------------------------------------------------- | :---------------------: |
| customer_id | The id of the customer whose orders you would like to retrieve. | :ballot_box_with_check: |

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
