# WordPress Custom Post Types: Exposing Meta Fields to REST API

This cheat sheet provides detailed steps to expose meta fields of a custom post type to the WordPress REST API in a grouped format, making it easier to access them. 

## Step 1: Register Meta Fields

First, register all the required meta fields for your custom post type using `register_post_meta`.

```php
add_action( 'init', 'register_my_meta' );
function register_my_meta() {
    // Define an array of meta keys to be registered.
    $meta_keys = ['my_meta_key1', 'my_meta_key2', 'my_meta_key3']; // Add more meta keys as needed

    // Loop through each meta key and register it.
    foreach ($meta_keys as $meta_key) {
        register_post_meta( 'my_custom_post_type', $meta_key, array(
            'show_in_rest' => true,
            'single' => true,
            'type' => 'string', // Adjust the type as necessary for each meta key
        ));
    }
}
```
## Step 2: Add Grouped Meta Fields to REST API Response
Instead of adding each meta field separately, group them in a single REST field.
```
add_action( 'rest_api_init', 'add_meta_group_to_rest_api' );
function add_meta_group_to_rest_api() {
    // Register a single REST field to group all meta fields.
    register_rest_field( 'my_custom_post_type', 'meta_fields', array(
        'get_callback' => 'get_all_meta_for_rest',
        'schema' => null,
    ));
}

// Function to retrieve all meta values as an array.
function get_all_meta_for_rest( $object ) {
    // Define the same meta keys registered earlier.
    $meta_keys = ['my_meta_key1', 'my_meta_key2', 'my_meta_key3']; // Add more meta keys as needed

    // Prepare an array to hold all meta values.
    $meta_values = [];
    foreach ($meta_keys as $meta_key) {
        // Fetch each meta value and add it to the array.
        $meta_values[$meta_key] = get_post_meta( $object['id'], $meta_key, true );
    }

    return $meta_values;
}
```
## Step 3: Testing
Test the updated implementation:

Access http://yoursite.com/wp-json/wp/v2/my_custom_post_type from a browser or API client.
Check if the meta fields are grouped together in the API response under meta_fields.
