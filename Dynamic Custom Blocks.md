# Implementing Dynamic Custom Blocks in WordPress

This cheat sheet focuses on implementing dynamic custom blocks in WordPress, assuming the initial setup and scaffolding of the block are already completed.

## Overview

Dynamic custom blocks interact with the WordPress database or use PHP to dynamically generate content. This guide dives directly into the implementation aspects.

## Step 1: Preparing for Dynamic Content

Start by identifying the dynamic content your block will handle. This could be content fetched from the database, dynamically generated based on user input, or any server-side processed content.

## Step 2: Modifying the Block JSON Configuration

Edit your block's `block.json` file to include a `render_callback` function. This function will handle the server-side rendering of the block.

```json
{
    "apiVersion": 2,
    "name": "create-block/my-custom-block",
    "title": "My Custom Block",
    ...
    "render_callback": "my_custom_block_render_callback"
}
```
## Step 3: Creating the PHP Render Callback Function
In your plugin's main PHP file, create the render callback function. This function will output the dynamic content of the block.
```
function my_custom_block_render_callback( $attributes, $content ) {
    // This function is called to render the content of your block on the server-side.
    // $attributes contains the attributes of the block defined in block.json or set by the user.
    // $content contains any content that is saved within the block. For dynamic blocks, this is often null.

    // Example: Fetching data from a custom post type 'my_custom_post_type'
    $args = array(
        'post_type'      => 'my_custom_post_type', // Specify your custom post type
        'posts_per_page' => 5, // Number of posts to retrieve
    );

    $query = new WP_Query($args);

    // Start an output buffer to capture the HTML content.
    ob_start();

    // Check if the query has posts.
    if ( $query->have_posts() ) {
        // Start a loop to iterate through the posts.
        while ( $query->have_posts() ) {
            $query->the_post();

            // Here you can output the HTML for each post.
            // For example, displaying the post title and excerpt.
            echo '<div class="my-custom-block-post">';
            echo '<h2>' . get_the_title() . '</h2>'; // Display the title of the post
            echo '<p>' . get_the_excerpt() . '</p>';  // Display the excerpt of the post
            echo '</div>';
        }

        // Reset post data to avoid conflicts with other queries.
        wp_reset_postdata();
    } else {
        // Output a message if no posts were found.
        echo '<p>No posts found.</p>';
    }

    // Get the content from the output buffer.
    $output = ob_get_clean();

    // Return the content to be displayed in the block.
    return $output;
}
```
- We're fetching the latest five posts of a custom post type named my_custom_post_type.
- The content of each post (title and excerpt) is displayed in a simple HTML structure.
- We use an output buffer (ob_start() and ob_get_clean()) to capture and return the HTML content efficiently.
- This function can be further customized to display any dynamic content based on the block's attributes or other WordPress data.

## Step 4: Registering the Block with the Render Callback
Ensure your block type is registered with the render callback function. 
### If The Block Is Not Registered Elsewhere
```
function register_my_custom_block() {
    register_block_type( __DIR__ . '/build/my-custom-block', array(
        'render_callback' => 'my_custom_block_render_callback',
    ));
}
add_action( 'init', 'register_my_custom_block' );
```
### If The Block Is Already Registered, Update With New Render Callback
```
function agent_register_block() {
    // Check if the block is already registered
    if ( ! WP_Block_Type_Registry::get_instance()->is_registered( 'real-estate-theme-tools/agent-block' ) ) {
        return;
    }

    // Get the existing block type
    $block_type = WP_Block_Type_Registry::get_instance()->get_registered( 'real-estate-theme-tools/agent-block' );

    // Modify the block type to add the render callback
    $block_type->render_callback = 'agent_render_callback';
}
add_action( 'init', 'agent_register_block', 20 ); // Ensure this runs after the initial block registration
```
## Step 5: Handling Block Attributes
Define any necessary attributes in your block.json file. These attributes can be accessed within the render callback function to customize the block's output.

## Step 6: Adding Server-Side Logic
Implement the server-side logic in the render callback function. This could involve querying the database, processing data, or generating dynamic HTML content based on the block's attributes.

## Step 7: Testing and Debugging
Test the block in various scenarios and ensure the dynamic content is rendered correctly both in the editor and on the front end.
Utilize debugging techniques, such as logging and breakpoints, to troubleshoot any issues.
