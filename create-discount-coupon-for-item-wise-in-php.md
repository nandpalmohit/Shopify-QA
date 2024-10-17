## Create Discount coupon through an API for item wise

To create an operation in PHP that applies a discount to products on a Shopify store when a button is clicked, you can leverage Shopify's Admin API to apply discounts dynamically. In this scenario, we'll implement a process where, upon clicking the button, a request is made to your server (PHP backend), which creates a price rule and discount code, and then applies it to the user's cart.

### Steps for the PHP-based solution:

1.  **Set up Shopify API credentials**: Get your Shopify API credentials (API key, password, and store name).
2.  **Create a price rule and discount code dynamically**: This code will create a discount based on specific conditions.
3.  **Apply the discount**: After the discount code is created, redirect the user to the checkout page with the discount applied.

### Step 1: Set Up Shopify API Credentials

You need to create a **private app** in your Shopify store to obtain API credentials. The private app will provide the necessary `API_KEY`, `PASSWORD`, and `SHOPIFY_STORE`.

### Step 2: PHP Code to Create a Price Rule and Discount

You can create a PHP file that will interact with Shopify's API to create a price rule and apply a discount code. Here’s how to do it.

#### PHP Code for Creating a Price Rule and Discount Code

##### php

    <?php
    // Set up Shopify API credentials
    define('SHOPIFY_API_KEY', 'your_api_key');
    define('SHOPIFY_PASSWORD', 'your_password');
    define('SHOPIFY_STORE', 'your_shopify_store');
    
    // Function to make API requests
    function shopifyRequest($endpoint, $method = 'GET', $data = null) {
        $url = "https://" . SHOPIFY_API_KEY . ":" . SHOPIFY_PASSWORD . "@" . SHOPIFY_STORE . ".myshopify.com/admin/api/2023-10" . $endpoint;
        
        $headers = [
            'Content-Type: application/json',
            'Accept: application/json'
        ];
    
        $ch = curl_init($url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
        curl_setopt($ch, CURLOPT_CUSTOMREQUEST, $method);
    
        if ($data) {
            curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($data));
        }
    
        $response = curl_exec($ch);
        curl_close($ch);
        
        return json_decode($response, true);
    }
    
    // Function to create a Price Rule
    function createPriceRule() {
        $price_rule_data = [
            'price_rule' => [
                'title' => '10% Off Discount',
                'target_type' => 'line_item',
                'target_selection' => 'entitled',
                'allocation_method' => 'across',
                'value_type' => 'percentage',
                'value' => '-10.0', // 10% discount
                'customer_selection' => 'all',
                'starts_at' => date('Y-m-d\TH:i:s\Z'), // Start now
                'entitled_product_ids' => [123456789], // Product IDs (replace with actual product IDs)
                'usage_limit' => 1,
                'once_per_customer' => true
            ]
        ];
    
        return shopifyRequest('/price_rules.json', 'POST', $price_rule_data);
    }
    
    // Function to create a Discount Code for the Price Rule
    function createDiscountCode($price_rule_id) {
        $discount_code_data = [
            'discount_code' => [
                'code' => 'YOURDISCOUNT' // Change as per your requirements
            ]
        ];
    
        return shopifyRequest("/price_rules/{$price_rule_id}/discount_codes.json", 'POST', $discount_code_data);
    }
    
    // Handle the button click and create a discount
    if ($_SERVER['REQUEST_METHOD'] === 'POST') {
        // Step 1: Create the Price Rule
        $price_rule_response = createPriceRule();
        
        if (isset($price_rule_response['price_rule']['id'])) {
            $price_rule_id = $price_rule_response['price_rule']['id'];
            
            // Step 2: Create a Discount Code
            $discount_code_response = createDiscountCode($price_rule_id);
            
            if (isset($discount_code_response['discount_code']['code'])) {
                $discount_code = $discount_code_response['discount_code']['code'];
                
                // Redirect to checkout with discount applied
                $shopify_store_url = "https://" . SHOPIFY_STORE . ".myshopify.com";
                header("Location: {$shopify_store_url}/discount/{$discount_code}");
                exit;
            } else {
                echo "Failed to create discount code.";
            }
        } else {
            echo "Failed to create price rule.";
        }
    }
    ?> 

### Step 3: HTML Form with Button

You can now create a simple form with a button that triggers this PHP script.

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Apply Discount</title>
        <style> .discount-button {
                padding: 10px 20px;
                background-color: #28a745;
                color: white;
                border: none;
                cursor: pointer;
                font-size: 16px;
            }
            .discount-button:hover {
                background-color: #218838;
            } </style>
    </head>
    <body>
        <form action="your_php_script.php" method="POST">
            <button type="submit" class="discount-button">Apply Discount</button>
        </form>
    </body>
    </html> 

### How it Works:

1.  **Price Rule Creation**: When the button is clicked, it triggers the PHP script, which creates a price rule for a 10% discount on the specified product.
2.  **Discount Code Creation**: The script then generates a discount code.
3.  **Redirect to Checkout**: Once the discount code is generated, the user is redirected to the Shopify store checkout with the discount applied.

### Notes:

-   Replace `'your_api_key'`, `'your_password'`, and `'your_shopify_store'` with your actual Shopify credentials.
-   You can adjust the discount value and the products (`entitled_product_ids`) to match your specific use case.
