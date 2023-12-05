
# WordPress Custom Post Types: Exposing Meta Fields to REST API

This cheat sheet provides detailed steps to expose meta fields of a custom post type to the WordPress REST API. This is essential for headless WordPress implementations where the frontend interacts with the WordPress backend via REST API.

## Step 1: Register Meta Field

First, register a meta field for your custom post type. This is done using `register_post_meta`.

```php
add_action( 'init', 'register_my_meta' );
function register_my_meta() {
    // Register a meta field for a custom post type.
    // - 'my_custom_post_type' should be replaced with your custom post type.
    // - 'my_meta_key' is the key for the meta field.
    // - The array contains settings for the meta field:
    //    - 'show_in_rest' => true makes it available in the REST API.
    //    - 'single' => true indicates if the meta field is a single value.
    //    - 'type' => 'string' specifies the data type (e.g., 'string', 'number').
    register_post_meta( 'my_custom_post_type', 'my_meta_key', array(
        'show_in_rest' => true,
        'single' => true,
        'type' => 'string', // Change the type based on your meta field
    ) );
}
```
## Step 2: Add Meta Field to REST API Response
After registering the meta field, ensure it is included in the REST API response.
```
add_action( 'rest_api_init', 'add_meta_to_rest_api' );
function add_meta_to_rest_api() {
    // Register the REST field for the custom post type.
    // - 'my_custom_post_type' should be your custom post type.
    // - 'my_meta_key' is the meta field key.
    // - The array specifies how to handle the field in the REST API.
    //    - 'get_callback' => 'get_my_meta_for_rest' specifies the function to retrieve the meta value.
    //    - 'schema' => null means there is no specific schema defined for this field.
    register_rest_field( 'my_custom_post_type', 'my_meta_key', array(
        'get_callback'    => 'get_my_meta_for_rest',
        'schema'          => null,
    ) );
}

// Function to retrieve the meta value.
function get_my_meta_for_rest( $object ) {
    // Fetches the meta value using get_post_meta.
    // - $object['id'] is the ID of the current post.
    // - 'my_meta_key' is the meta field key.
    // - true specifies to return a single value.
    return get_post_meta( $object['id'], 'my_meta_key', true );
}
```
## Step 3: Testing
Finally, test the implementation.

Access http://yoursite.com/wp-json/wp/v2/my_custom_post_type from a browser or API client.
Check if the meta fields are correctly appearing in the API response.
