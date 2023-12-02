# WordPress Custom Block Creation Cheat Sheet

## Table Of Contents
- [Step 1: Setting Up Your Development Environment](#step-1-setting-up-your-development-environment)
  - [1.1. Install a Local Server Environment](#11-install-a-local-server-environment)
  - [1.2. Install WordPress Locally](#12-install-wordpress-locally)
  - [1.3. Manually Creating a Multi-Block WordPress Plugin](#13-manually-creating-a-multi-block-wordpress-plugin)
  - [1.4. Install a Code Editor](#14-install-a-code-editor)
  - [1.5. WordPress Environment Check](#15-wordpress-environment-check)
- [Step 2: Creating a Custom Plugin for Your Block](#step-2-creating-a-custom-plugin-for-your-block)
  - [2.1. Create a New Plugin Directory](#21-create-a-new-plugin-directory)
  - [2.2. Create the Main Plugin File](#22-create-the-main-plugin-file)
  - [2.3. Create JavaScript and CSS Files](#23-create-javascript-and-css-files)
  - [2.4. Write Basic Block JavaScript](#24-write-basic-block-javascript)
- [Step 3: Registering a New Block in WordPress](#step-3-registering-a-new-block-in-wordpress)
  - [3.1. Register Block Type in PHP](#31-register-block-type-in-php)
  - [3.2. Edit JavaScript Block Registration](#32-edit-javascript-block-registration)
  - [3.3. Enqueue Editor Styles](#33-enqueue-editor-styles)
- [Step 4: Creating the Block's Edit and Save Functions](#step-4-creating-the-blocks-edit-and-save-functions)
  - [4.1. The Edit Function](#41-the-edit-function)
  - [4.2. The Save Function](#42-the-save-function)
- [Step 5: Enqueuing Block Assets (JavaScript and CSS)](#step-5-enqueuing-block-assets-javascript-and-css)
  - [5.1. Enqueue JavaScript File](#51-enqueue-javascript-file)
  - [5.2. Enqueue CSS File](#52-enqueue-css-file)
- [Step 6: Adding Block Attributes](#step-6-adding-block-attributes)
- [Step 7: Testing and Debugging Your Block](#step-7-testing-and-debugging-your-block)
  - [7.1. Testing Your Block](#71-testing-your-block)
  - [7.2. Debugging Your Block](#72-debugging-your-block)
  - [7.3. Common Issues and Solutions](#73-common-issues-and-solutions)


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

### 1.3. Manually Creating a Multi-Block WordPress Plugin

#### 1.3.1. Setting Up the Plugin Directory

- Create a new directory in `wp-content/plugins` for your plugin.
- Inside this directory, create a PHP file for your plugin (e.g., `my-multi-block-plugin.php`) with the necessary WordPress plugin header information.

#### 1.3.2. Initializing Node.js and NPM

- Inside your plugin directory, initialize a Node.js project:

  ```bash
  npm init -y
  ```
- This creates a package.json file in your directory.
- 
### 1.3.3. Installing Build Tools
Install Webpack, Babel, and other necessary build tools:
```
npm install --save-dev webpack webpack-cli @babel/core babel-loader @babel/preset-env @wordpress/scripts
```
### 1.3.4. Creating Block Directories
- Create a src directory within your plugin folder.
- Inside src, create separate directories for each block (e.g., block1, block2).

### 1.3.5. Adding Block Files
- In each block directory, add JavaScript and CSS files (e.g., index.js, editor.css, style.css).

###1.3.6. Configuring Webpack
- Create a webpack.config.js file in your plugin root.

Configure Webpack to have multiple entry points, one for each block:
```
module.exports = {
  entry: {
    block1: './src/block1/index.js',
    block2: './src/block2/index.js',
    // Add more blocks as needed
  },
  // Further configuration...
};
```
### 1.3.7. Setting Up NPM Scripts
- Modify your package.json to include scripts for start and build:
```
"scripts": {
  "start": "webpack --watch",
  "build": "webpack --mode production"
}
```
### 1.3.8. Running the Development and Production Builds
Use npm start for development. This will watch your files for changes and recompile as needed.
Use npm run build for creating production builds.

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

### 2.2. Create the Main Plugin Files

Inside your new directory, create the main PHP file for the plugin. This file will contain plugin metadata and initialization code.
Also, create an index.js file for importing the individuals blocks.

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
        'my-custom-block-js',
        plugins_url('build/index.js', __FILE__),
        array('wp-blocks', 'wp-i18n', 'wp-element', 'wp-editor'),
        filemtime(plugin_dir_path(__FILE__) . 'build/index.js')
    );

    wp_enqueue_style(
        'my-custom-block-editor-css',
        plugins_url('build/index.css', __FILE__), // If you have a main CSS file
        array('wp-edit-blocks'),
        filemtime(plugin_dir_path(__FILE__) . 'build/index.css')
    );
}

add_action('enqueue_block_assets', 'my_custom_block_assets');
add_action('enqueue_block_editor_assets', 'my_custom_block_assets');


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

The `edit` and `save` functions in your block's JavaScript file are essential for defining the block's behavior in the editor and its appearance on the website.

### 4.1. The Edit Function

The `edit` function is responsible for rendering the block within the WordPress editor. It's where you define the block's interactive editing interface.

- Update your `block.js` file with the following `edit` function:

```javascript
const { __ } = wp.i18n; // Import __() from wp.i18n for internationalization
const { RichText } = wp.blockEditor; // Import RichText component from wp.blockEditor

registerBlockType('my-custom-block/my-block', {
    // ...other properties

    edit: ({ attributes, setAttributes, className }) => {
        const { content } = attributes;

        const onChangeContent = newContent => {
            setAttributes({ content: newContent });
        };

        return (
            <RichText
                tagName="p" // The tag here is the HTML element to be used for the editable part.
                className={className}
                value={content}
                onChange={onChangeContent}
                placeholder={__('Enter your content here', 'my-custom-block')}
            />
        );
    },

    // ...save function
});
```
- This code uses the RichText component to create an editable text area.
- The setAttributes function is used to save the changes made by the user.

### 4.2. The Save Function
The save function defines how the block's content is saved and then rendered on the frontend.

- Continue editing your block.js file with the save function:
```
const { RichText } = wp.blockEditor; // Import RichText component from wp.blockEditor

registerBlockType('my-custom-block/my-block', {
    // ...other properties

    // ...edit function

    save: ({ attributes }) => {
        const { content } = attributes;

        return (
            <RichText.Content
                tagName="p" // The tag here matches the tag used in the edit function.
                value={content}
            />
        );
    },
});
```
- The save function returns the HTML structure that will be saved to the post content.
- The RichText.Content component outputs the saved content.
- 
## Step 5: Enqueuing Block Assets (JavaScript and CSS)

Enqueuing assets is crucial for adding JavaScript and CSS to your custom block. This step ensures that your block's scripts and styles are loaded correctly in the WordPress editor and on the front end.

### 5.1. Enqueue JavaScript File

First, you need to enqueue the JavaScript file that contains your block's functionality.

- Edit your main plugin file, `my-custom-block.php`.
- Add the following PHP function to enqueue your JavaScript file:

```php
function my_custom_block_enqueue() {
    // Enqueue the block's script
    wp_enqueue_script(
        'my-custom-block-js', // Handle for the script.
        plugins_url('/build/index.js', __FILE__), // Path to the JS file.
        array('wp-blocks', 'wp-element', 'wp-editor'), // Dependencies.
        filemtime(plugin_dir_path(__FILE__) . '/build/index.js') // Version: file modification time.
    );
}

add_action('enqueue_block_assets', 'my_custom_block_enqueue');
```
- This function hooks into WordPress to enqueue your block script.
- Ensure the script dependencies and path are correctly set.

### 5.2. Enqueue CSS File
Next, enqueue the CSS file to style your block in the editor and on the front end.

Continue editing my-custom-block.php.
Add the following code to enqueue your CSS file:
```
function my_custom_block_enqueue() {
    // ...JavaScript enqueue code...

    // Enqueue the block's CSS
    wp_enqueue_style(
        'my-custom-block-css', // Handle for the stylesheet.
        plugins_url('/build/style.css', __FILE__), // Path to the CSS file.
        array(), // Dependencies (if any).
        filemtime(plugin_dir_path(__FILE__) . '/build/style.css') // Version: file modification time.
    );
}

add_action('enqueue_block_assets', 'my_custom_block_enqueue');
```
- This code adds your block's stylesheet to the WordPress site.
- It's important to specify the correct path to your CSS file.
By following these steps, you ensure that your custom block's JavaScript and CSS are properly enqueued and loaded where they're needed.

## Step 6: Adding Block Attributes

Block attributes are key components in WordPress blocks, serving as the primary means for blocks to store and manipulate data. They're akin to variables in programming, holding information that can change over time based on user interactions or other factors.

### 6.1. Understanding the Importance of Attributes

Attributes play a vital role in:

- **Data Storage**: They hold the data that gets saved in the database. For instance, the text in a paragraph block or the URL in an image block.
- **Dynamic Content**: Attributes enable your block to interact dynamically with user inputs and other changes, making the block versatile and adaptable.
- **State Management**: They help in managing the state of the block, especially when the block's content changes. This is crucial for a seamless user experience in the editor.
- **Customization and Configuration**: Attributes allow users to configure the block as per their needs, be it changing text, selecting options, or customizing styles.

### 6.2. Define Block Attributes

To effectively use attributes in your block, define them in your block's JavaScript file.

- Update your `block.js` file to include attribute definitions:

```javascript
registerBlockType('my-custom-block/my-block', {
    // ...other properties

    attributes: {
        content: {
            type: 'string', // The type of data (e.g., string, number, boolean)
            source: 'html', // Defines how to extract the value from the saved content
            selector: 'p', // CSS selector to target the right element
        },
        // Add more attributes as needed
    },

    // ...edit and save functions
});
```
### 6.3. Use Attributes in Edit and Save Functions

Incorporate these attributes in your edit and save functions to manage the block's data.

Modify the edit function to utilize the content attribute:
```
edit: ({ attributes, setAttributes }) => {
    const { content } = attributes;

    const onChangeContent = newContent => {
        setAttributes({ content: newContent }); // Updates the attribute when content changes
    };

    return (
        <RichText
            // ...other properties
            value={content}
            onChange={onChangeContent}
        />
    );
},

```
- Update the save function to reflect attribute changes:
```
save: ({ attributes }) => {
    const { content } = attributes;

    return (
        <RichText.Content
            // ...other properties
            value={content}
        />
    );
},
```
Attributes thus become the backbone of your block's functionality, allowing it to store and display data dynamically based on user interaction.


## Step 7: Testing and Debugging Your Block

Testing and debugging are critical phases in the development of your custom block. They ensure your block functions as intended and help identify any issues that need to be resolved.

### 7.1. Testing Your Block

Testing involves checking your block's functionality in various scenarios and ensuring it behaves as expected. 

- **Manual Testing**: Interact with your block in the Gutenberg editor. Add it to a post, change its attributes, and see how it behaves.
- **Visual Testing**: Ensure your block renders correctly in both the editor and the front end. Pay attention to styling and layout.
- **Console Logging**: Use `console.log()` in your JavaScript code to output attribute values and other important information to the browser's console. This helps in understanding the block's state at different stages.
- **Responsiveness**: Test your block on different screen sizes to ensure it's responsive and looks good on mobile, tablet, and desktop views.

### 7.2. Debugging Your Block

When you encounter issues or unexpected behavior, debugging is necessary to find and fix the problems.

- **JavaScript Debugging**: Use browser developer tools to debug your JavaScript code. Set breakpoints, step through your code, and inspect variables.
- **PHP Debugging**: If your block involves PHP code, enable WP_DEBUG in your `wp-config.php` to see PHP errors, warnings, and notices.
- **Error Logs**: Check WordPress and server error logs for any relevant error messages that can give clues about the issue.
- **Community Forums and Documentation**: Utilize the WordPress community forums and developer documentation for solutions to common problems.

### 7.3. Common Issues and Solutions

- **Block Not Appearing**: Ensure your block is correctly registered, and the JavaScript and PHP files are correctly enqueued.
- **Styling Issues**: Verify that your CSS files are properly enqueued and the styles are correctly applied.
- **Attribute Errors**: Check if attributes are correctly defined and used in both edit and save functions.

Remember, thorough testing and effective debugging are key to creating a robust and reliable custom block for WordPress.
