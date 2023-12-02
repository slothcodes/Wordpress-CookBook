# Custom WordPress Block Development Recipes

## Table of Contents
1. [Setting Up Custom Gutenberg Blocks](#1-setting-up-custom-gutenberg-blocks)
    1. [Plugin Structure](#a-plugin-structure)
    2. [Folder and File Setup](#b-folder-and-file-setup)
    3. [Initializing NPM and Git](#c-initializing-npm-and-git)
    4. [Connecting to GitHub](#d-connecting-to-github)
    5. [Enqueueing Styles and Scripts](#e-enqueueing-styles-and-scripts)
    6. [Creating Dynamic Blocks](#f-creating-dynamic-blocks)
    7. [Server-Side Rendering](#g-server-side-rendering)
    8. [Setting Up React and Build Processes](#h-setting-up-react-and-build-processes)

## 1. Setting Up Custom Gutenberg Blocks

### A. Plugin Structure
Organize your plugin with a main PHP file and a 'blocks' folder for block files.

```php
// Structure of your plugin directory
plugin-name/
├── plugin-name.php       // Main plugin PHP file
└── blocks/               // Folder for Gutenberg blocks
    ├── block-1/
    │   ├── index.js      // JavaScript file for block functionality
    │   ├── style.scss    // SCSS file for front-end styles
    │   └── editor.scss   // SCSS file for editor styles
    └── block-2/
        ├── index.js
        ├── style.scss
        └── editor.scss
```
The main plugin file (plugin-name.php) is responsible for initializing your custom Gutenberg blocks and any other plugin functionality.
### B. Folder and File Setup
Set up individual directories for each block within the 'blocks' folder and include a main plugin file.
```
// Main plugin file: plugin-name.php
<?php
/**
 * Plugin Name: Your Plugin Name
 * Description: A description of your plugin.
 * Version: 1.0
 * Author: Your Name
 */

// Your plugin's main code goes here

// Include enqueue functions and block registrations

```

### C. Initializing NPM and Git
Initialize NPM and Git for dependency management and version control.
```# Navigate to your plugin directory
cd path/to/your-plugin

# Initialize NPM to manage JavaScript dependencies
npm init -y

# Initialize Git for version control
git init
```
### D. Connecting to GitHub
Link your local repository with a GitHub repository for remote version control.
```# Link to your GitHub repository
git remote add origin https://github.com/your-username/your-repository.git

# Stage all current files for commit
git add .

# Commit the files with a message
git commit -m "Initial commit"

# Push the commit to GitHub
git push -u origin master
```
### E. Enqueueing Styles and Scripts
In your main plugin file, enqueue the JavaScript and CSS files for your blocks.
```
<?php
// Function to enqueue block assets
function enqueue_block_assets() {
    // Enqueue the block editor script
    wp_enqueue_script(
        'example-block-editor-script', // Handle for the script
        plugins_url( 'blocks/example-block/index.js', __FILE__ ), // Path to the block editor JS
        array( 'wp-blocks', 'wp-i18n', 'wp-element', 'wp-editor' ), // Script dependencies
        filemtime( plugin_dir_path( __FILE__ ) . 'blocks/example-block/index.js' ) // Version number
    );

    // Enqueue the block editor style
    wp_enqueue_style(
        'example-block-editor-style', // Handle for the style
        plugins_url( 'blocks/example-block/editor.css', __FILE__ ), // Path to the block editor CSS
        array( 'wp-edit-blocks' ), // Style dependencies
        filemtime( plugin_dir_path( __FILE__ ) . 'blocks/example-block/editor.css' ) // Version number
    );

    // Enqueue the front-end style
    wp_enqueue_style(
        'example-block-style', // Handle for the style
        plugins_url( 'blocks/example-block/style.css', __FILE__ ), // Path to the block style CSS
        array(), // No dependencies
        filemtime( plugin_dir_path( __FILE__ ) . 'blocks/example-block/style.css' ) // Version number
    );
}
add_action( 'enqueue_block_assets', 'enqueue_block_assets' );
?>
```
### F. Creating Dynamic Blocks
Dynamic blocks are rendered on the server. Create a PHP callback function for rendering and register it in index.js.
```
<?php
// Server-side render callback for the block
function render_dynamic_block( $attributes ) {
    // Render the block's HTML from the attributes
    return '<div>' . esc_html( $attributes['content'] ) . '</div>';
}

// Register the block
register_block_type( 'your-plugin/example-block', array(
    'render_callback' => 'render_dynamic_block',
) );
?>
```
In index.js, register the block with registerBlockType and specify the edit and save functions.
```
// Example block registration
const { registerBlockType } = wp.blocks;

registerBlockType( 'your-plugin/example-block', {
    // Block configuration
    edit: function( props ) {
        // Editor view
    },
    save: function() {
        // Return null as this is a dynamic block
        return null;
    }
});
```
### G. Server-Side Rendering

Server-side rendering for Gutenberg blocks involves rendering the block's dynamic content with PHP. This is particularly useful for blocks whose content depends on external data or needs to be dynamically updated.

#### PHP Side: Registering the Block with a Render Callback

In your plugin's main PHP file, register the block and specify a server-side rendering callback.

```php
<?php
// Include WordPress core function library
include_once( ABSPATH . 'wp-admin/includes/plugin.php' );

// Function to register the block and its server-side render callback
function register_my_custom_block() {
    // Check if the function exists
    if ( function_exists( 'register_block_type' ) ) {
        // Register the block
        register_block_type( 'your-plugin/your-custom-block', array(
            // Specify the server-side rendering callback
            'render_callback' => 'my_custom_block_render_callback',
        ));
    }
}
add_action( 'init', 'register_my_custom_block' ); // Hook into WordPress init action

// The server-side rendering callback function
function my_custom_block_render_callback( $attributes, $content ) {
    // $attributes contains block attributes
    // $content contains any nested blocks or default content

    // Generate the block output
    $block_content = '<div class="my-custom-block">' . esc_html( $attributes['message'] ) . '</div>';

    // Return the rendered content
    return $block_content;
}
```
### JavaScript Side: Block Registration without Save Method
In your block's JavaScript file (index.js), define the block without a save method since it's rendered via PHP.
```
// JavaScript for registering the block
const { registerBlockType } = wp.blocks;

registerBlockType( 'your-plugin/your-custom-block', {
    // Block title, icon, and category
    title: 'My Custom Block',
    icon: 'smiley',
    category: 'common',

    // Define attributes used in your block
    attributes: {
        message: {
            type: 'string',
            default: 'Hello World',
        },
    },

    // Editor view configuration
    edit: function( props ) {
        // Function to update block attributes
        const onChangeMessage = ( newMessage ) => {
            props.setAttributes( { message: newMessage } );
        };

        // Editor view HTML
        return (
            <div className="my-custom-block">
                <textarea 
                    value={ props.attributes.message }
                    onChange={ ( event ) => onChangeMessage( event.target.value ) }
                />
            </div>
        );
    },

    // Save function is not needed for server-side rendered blocks
    save: () => null,
});

```
### H. Setting Up React and Build Processes

Integrating React for block development and establishing build processes are crucial for efficient development and performance optimization.

#### React Setup

1. **Install React and Babel Dependencies**

   Ensure that your `package.json` includes React and Babel. Run the following command in your plugin directory:

   ```bash
   npm install --save react react-dom @babel/core @babel/preset-env @babel/preset-react babel-loader
   ```
### Configure Babel

Create a .babelrc file in your plugin directory with the following configuration:
```
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```
### Sample React Component for Block

In your block's index.js, use React components for rendering the block's edit and save functions.
```
import { registerBlockType } from '@wordpress/blocks';
import { useBlockProps, RichText } from '@wordpress/block-editor';

registerBlockType('your-plugin/your-custom-block', {
  // Block configuration...

  edit: ({ attributes, setAttributes }) => {
    return (
      <div {...useBlockProps()}>
        <RichText
          tagName="p"
          value={attributes.content}
          onChange={(content) => setAttributes({ content })}
        />
      </div>
    );
  },

  save: ({ attributes }) => {
    return (
      <div {...useBlockProps.save()}>
        <RichText.Content tagName="p" value={attributes.content} />
      </div>
    );
  },
});
```
## Build Process Setup

### Webpack Configuration
Set up Webpack to bundle your JavaScript and SCSS files. 
Create a webpack.config.js file with the necessary configuration for React, Babel, and SCSS processing.

### NPM Scripts for Development and Production

Modify your package.json to include scripts for development and production builds.
```
"scripts": {
  "start": "webpack --watch",
  "build": "webpack --mode production"
}
```
### Running the Build Process

Use npm start to start the development build process. It will watch for file changes and recompile as needed.
Use npm run build to create a production build.
