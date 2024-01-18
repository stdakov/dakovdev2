+++
title = "Catch Paypal donation response"
date = 2024-01-17
description = "Catch paypal donation success response"
+++

So you have a donation button and a success donation page? Ok, let's catch the response then!

I have read a lot of articles but didn't find what I needed. 

Let's first create a donation form. Go to the Paypal button page and there is a donation button generator.\
Just go through all the steps and at the end should have HTML form like this: 

```html
<form action="https://www.paypal.com/donate" method="post" target="_top" class=" justify-content-center">
    <input type="hidden" name="hosted_button_id" value="{This value comes from the paypal}"/>
    <input type="hidden" name="custom" value="Some custom data that you need to transfer to the success page"/>
    <input type="hidden" name="item_name" value="This will overwrite the message in the donation popup"/>
    <input type="image" style="width: 180px;" src="/media/images/donate.gif" name="submit" title="PayPal - The safer, easier way to pay online!" alt="Donate with PayPal button"/>
</form>
```

- name="**hosted_button_id**" - this contains the unique identifier of the hosted button **[required]**
- name="**custom**" - if you need to transfer data that is hidden for the user, you can use this field **[optional]**
- name="**item_name**" - can overwrite the message for the user in the donation form. **[optiona]**

## Paypal donation response:

On successful donation, the user can go to the success page via a link on the PayPal success page.\
When the user clicks on that link PayPal redirects the user to that page. \
PayPal sends information about the donation transaction in GET Parameters. \
The serialized request should look like this:

```json
{
  "tx": "123123123123123",
  "st": "Completed",
  "amt": "5.00",
  "cc": "GBP",
  "cm": "Custom Data",
  "item_number": "",
  "item_name": "Thank you for the support!"
}
```
Here is how I tested it. \
I created a success page. I use PHP so I call it just success.php

Here is the PHP code:

```php
<?php
// Define the log file path
$logFile = 'http_requests.log';

// Get the current timestamp
$timestamp = date('Y-m-d H:i:s');

// Log the HTTP method and timestamp
$logData = $_SERVER['REQUEST_METHOD'] . " request at $timestamp";

// If it's a GET request, log the parameters
if ($_SERVER['REQUEST_METHOD'] === 'GET') {
    $logData .= " - Parameters: " . json_encode($_GET);
}

// If it's a POST request, log the POST data
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $logData .= " - POST Data: " . json_encode($_POST);
}

// Append a new line and write to the log file
file_put_contents($logFile, $logData . PHP_EOL, FILE_APPEND);

// Your regular PHP code goes here...

// For demonstration purposes, you can send a response back
echo "Success!";
?>

```