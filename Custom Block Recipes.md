# WordPress Custom Block Creation Cheat Sheet

## Step 1: Setting Up Your Development Environment

To create a custom WordPress block, you first need to set up a local development environment. This involves installing WordPress locally and ensuring you have the necessary tools.

### 1.1. Install a Local Server Environment

You can use tools like XAMPP, MAMP, or Local by Flywheel. For this example, we'll use XAMPP:

- Download XAMPP from [https://www.apachefriends.org/index.html](https://www.apachefriends.org/index.html).
- Install XAMPP and start Apache and MySQL services.

### 1.2. Install WordPress Locally

- Download WordPress from [https://wordpress.org/download/](https://wordpress.org/download/).
- Extract the WordPress zip file into the `htdocs` directory of XAMPP.
- Rename the extracted folder to a project name of your choice (e.g., `my-block-plugin`).
- Create a new database for your WordPress site via the phpMyAdmin panel at `http://localhost/phpmyadmin`.
- Start WordPress installation by navigating to `http://localhost/my-block-plugin`.
- Follow the installation steps.

### 1.3. Set Up Node.js and NPM

You need Node.js and NPM to compile JavaScript and manage dependencies.

- Download and install Node.js from [https://nodejs.org/](https://nodejs.org/).
- Verify installation by running `node -v` and `npm -v` in your command line or terminal.

### 1.4. Install a Code Editor

Use a code editor like Visual Studio Code, Atom, or Sublime Text for coding. For instance, to use Visual Studio Code:

- Download it from [https://code.visualstudio.com/](https://code.visualstudio.com/).
- Install and open the editor.

### 1.5. WordPress Environment Check

Ensure you have the latest version of WordPress and that the Gutenberg plugin is installed and activated, as it offers the latest block editor features.

- Check WordPress version in the admin dashboard and update if necessary.
- Install the Gutenberg plugin from the WordPress plugin repository.


## Step 2: Creating a Custom Plugin for Your Block

Creating a custom plugin is essential for housing your custom block. This ensures your block is portable and independent of the theme.

### 2.1. Create a New Plugin Directory

First, create a directory for your plugin in the `wp-content/plugins` directory of your WordPress installation.

- Navigate to `wp-content/plugins`.
- Create a new directory named `my-custom-block`.

### 2.2. Create the Main Plugin File

Inside your new directory, create the main PHP file for the plugin. This file will contain plugin metadata and initialization code.

- Create a file named `my-custom-block.php`.
- Add the following content to the file:

```php
<?php
/**
 * Plugin Name: My Custom Block
 * Description: A custom block for Gutenberg.
 * Version: 1.0
 * Author: Your Name
 */

// Prevent direct access to the file
defined('ABSPATH') or die('No script kiddies please!');

/**
 * Load block assets for both frontend + backend.
 */
function my_custom_block_assets() {
    wp_enqueue_script(
        'my-custom-block-js', // Handle.
        plugins_url('block.js', __FILE__), // block.js: The block's JS.
        array('wp-blocks', 'wp-i18n', 'wp-element', 'wp-editor'), // Dependencies, should include 'wp-element'.
        filemtime(plugin_dir_path(__FILE__) . 'block.js') // Version: File modification time.
    );

    wp_enqueue_style(
        'my-custom-block-editor-css', // Handle.
        plugins_url('editor.css', __FILE__), // editor.css: Editor style.
        array('wp-edit-blocks'), // Dependency to include the CSS after it.
        filemtime(plugin_dir_path(__FILE__) . 'editor.css') // Version: File modification time.
    );
}

// Hook: Frontend assets.
add_action('enqueue_block_assets', 'my_custom_block_assets');

// Hook: Editor assets.
add_action('enqueue_block_editor_assets', 'my_custom_block_assets');
```
- This code registers a function to enqueue your block's JavaScript and CSS files.
- It uses wp_enqueue_script and wp_enqueue_style to load your assets.

### 2.3. Create JavaScript and CSS Files
Create the JavaScript and CSS files for your block. These files define the behavior and style of your block.

- Create a file named block.js in your plugin directory.
- Create a file named editor.css for editor-specific styles.

### 2.4. Write Basic Block JavaScript
Here's a basic structure for your block.js:
```
const { registerBlockType } = wp.blocks; // Import registerBlockType from wp.blocks

registerBlockType('my-custom-block/my-block', {
    title: 'My Custom Block',
    icon: 'smiley',
    category: 'common',
    edit() {
        return <div>Hello, World!</div>;
    },
    save() {
        return <div>Hello, World!</div>;
    },
});
```
- This JavaScript uses registerBlockType to define a new block.
- The edit function defines the editor interface.
- The save function defines the content saved to the database and displayed on the frontend.
## Step 3: Registering a New Block in WordPress

After setting up your plugin, the next step is to register your custom block within WordPress. This involves editing your plugin's PHP and JavaScript files.

### 3.1. Register Block Type in PHP

You need to inform WordPress about your new block and its script dependencies.

- Edit your `my-custom-block.php` file to include block registration.
- Add the following PHP code:

```php
function my_custom_block_register_block() {
    // automatically load dependencies and version
    $asset_file = include(plugin_dir_path(__FILE__) . 'build/index.asset.php');

    wp_register_script(
        'my-custom-block-editor-script', // Handle.
        plugins_url('build/index.js', __FILE__), // Block.build.js: We register the block here.
        $asset_file['dependencies'], // Dependencies.
        $asset_file['version'] // Version.
    );

    register_block_type('my-custom-block/my-block', array(
        'editor_script' => 'my-custom-block-editor-script', // We registered this above.
    ));
}

add_action('init', 'my_custom_block_register_block');
```
- This code uses register_block_type to register the block in WordPress.
- It sets the editor script to the handle of the script you enqueued earlier.

### 3.2. Edit JavaScript Block Registration
Modify the block.js (or index.js if you have a build process) to register your block with additional settings.
```
const { registerBlockType } = wp.blocks; // Import registerBlockType from wp.blocks

registerBlockType('my-custom-block/my-block', {
    title: 'My Custom Block',
    icon: 'smiley',
    category: 'common',
    attributes: {
        // Define attributes here if needed.
    },
    edit: function(props) {
        // Add editor code here.
        return <div className={props.className}>Hello, World (Editor)!</div>;
    },
    save: function() {
        // Add save code here.
        return <div>Hello, World (Frontend)!</div>;
    },
});
```
- The edit function defines the block as seen in the editor.
- The save function defines how the block looks on the front end.
- You can add block attributes in the attributes property.

### 3.3. Enqueue Editor Styles
Optionally, you can add styles specific to the block editor.
- In your my-custom-block.php file, enqueue an editor style:
```
function my_custom_block_editor_assets() {
    wp_enqueue_style(
        'my-custom-block-editor-style', // Handle for the style.
        plugins_url('editor.css', __FILE__), // Path to the CSS file.
        array(), // Dependency array.
        filemtime(plugin_dir_path(__FILE__) . 'editor.css') // Version: file modification time.
    );
}

add_action('enqueue_block_editor_assets', 'my_custom_block_editor_assets');
```
- This CSS file will only affect the block within the Gutenberg editor.
With these steps, your custom block is now registered in WordPress. You can start editing it in the Gutenberg editor and see how it behaves in the frontend.
## Step 4: Creating the Block's Edit and Save Functions
Detailed code for the edit and save functions of your block.

## Step 5: Enqueuing Block Assets (JavaScript and CSS)
Instructions for enqueuing JavaScript and CSS files for the block.

## Step 6: Adding Block Attributes
Explanation on how to add and handle block attributes.

## Step 7: Testing and Debugging Your Block
Tips on how to test and debug your custom block.

## Step 8: Packaging and Distributing Your Block
Guidance on packaging your block for distribution.

## Conclusion
Final thoughts and additional resources.
