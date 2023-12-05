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
// Define a function named 'agent_render_callback'. This function is a callback for rendering a block.
// It takes two parameters: $attributes (an array of attributes passed to the block) and $content (the content inside the block, if any).
function agent_render_callback( $attributes, $content ) {
    // Retrieve the 'selectedAgentId' attribute from the block's attributes and store it in $agent_id.
    $agent_id = $attributes['selectedAgentId'];

    // Get the WP_Post object for the post with ID equal to $agent_id.
    $agent = get_post( $agent_id );

    // Retrieve the 'agent_phone' meta field value for the post and store it in $agent_phone.
    // 'true' indicates that it returns a single value (string) rather than an array of values.
    $agent_phone = get_post_meta( $agent_id, 'agent_phone', true );

    // Retrieve the 'agent_first_name' meta field value for the post and store it in $agent_first_name.
    // Similar to above, 'true' indicates a single value is returned.
    $agent_first_name = get_post_meta( $agent_id, 'agent_first_name', true );

    // Start output buffering. This means that instead of directly outputting HTML, PHP will hold it in a buffer.
    ob_start();

    // Check if the $agent object is valid (i.e., if a post with the given ID was found).
    if ( $agent ) {
        // Start a div with the class 'agent-card'. This is likely for styling purposes.
        echo '<div class="agent-card">';

        // Start another div within 'agent-card' for agent information, with the class 'agent-info'.
        echo '<div class="agent-info">';

        // Output the agent's first name within an <h3> element, ensuring HTML characters are escaped for security.
        echo '<h3>Name: ' . esc_html($agent_first_name) . '</h3>';

        // Output the agent's phone number within a <p> element, with HTML characters escaped.
        echo '<p>Phone: ' . esc_html($agent_phone) . '</p>';

        // Close the 'agent-info' div.
        echo '</div>';

        // Close the 'agent-card' div.
        echo '</div>';
    } else {
        // If no agent is found, output a paragraph stating 'No Agents Found'.
        echo '<p>No Agents Found</p>';
    }

    // End output buffering and return the HTML content that was buffered.
    // This is the HTML that will be rendered by the block on the frontend.
    return ob_get_clean();
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
