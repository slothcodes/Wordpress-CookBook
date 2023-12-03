## Table of Contents
[TLDR Steps](#TLDR-Steps)
1. [Step 1: Registering a Custom Post Type](#step-1-registering-a-custom-post-type)
   - [Register Post Type Code](#register-post-type-code)
   - [Label Descriptions](#label-descriptions)
     - ['name' and '_x() Function](#name-and-_x-function)
     - ['singular_name' Label](#singular_name-label)
     - ['menu_name' Label](#menu_name-label)
     - [More Label Descriptions](#more-label-descriptions)
2. [Step 3: Customizing Your Custom Post Type](#step-3-customizing-your-custom-post-type)
   - [Customization Code](#customization-code)
   - [Explanation of Customizations](#explanation-of-customizations)
     - [Visibility and Access](#visibility-and-access)
     - [Permalinks and Rewrites](#permalinks-and-rewrites)
     - [Support for Features](#support-for-features)
     - [Hierarchical Structure](#hierarchical-structure)
     - [Archive Page Settings](#archive-page-settings)
     - [Capability Type](#capability-type)
     - [Menu Position and Icon](#menu-position-and-icon)

## TLDR Steps
### Steps to Initialize a Basic Custom Post Type
#### Step 1: Open Your Theme's Functions.php File
- Locate and open the functions.php file in your WordPress theme (preferably a child theme).
#### Step 2: Insert Custom Post Type Function
- At the end of the functions.php file, insert the custom post type function:
```
add_action('init', 'create_custom_post_type');

function create_custom_post_type() {
    // Add code for labels and arguments here
}
```
#### Step 3: Define Labels for the Post Type
- Inside the create_custom_post_type function, start by defining labels for your custom post type:
```
$labels = array(
    'name' => _x('Books', 'Post type general name', 'your-textdomain'),
    'singular_name' => _x('Book', 'Post type singular name', 'your-textdomain'),
    // Add additional labels as necessary
);
```
#### Step 4: Set Up Arguments for the Post Type
- Define the arguments ($args) for your custom post type:
```
$args = array(
    'labels' => $labels,
    'public' => true,
    // Include other arguments as needed
);
```
#### Step 5: Register the Post Type
- Complete the function by registering the post type:
```
register_post_type('book', $args);
```
#### Step 6: Save and Upload the Functions.php File
- Save the changes to your functions.php file and, if necessary, upload it to your server.
## Detailed Steps

### Step 1: Registering a Custom Post Type
Now, let's register a custom post type. We'll do this in the functions.php file of your child theme.
```
<?php
// Add your custom post type function to the 'init' action
add_action('init', 'create_custom_post_type');

/**
 * Function to create custom post type
 */
function create_custom_post_type() {
    // Labels for the post type
    $labels = array(
        'name' => _x('Books', 'Post type general name', 'your-textdomain'),
        'singular_name' => _x('Book', 'Post type singular name', 'your-textdomain'),
        'menu_name' => _x('Books', 'Admin Menu text', 'your-textdomain'),
        'name_admin_bar' => _x('Book', 'Add New on Toolbar', 'your-textdomain'),
        'add_new' => __('Add New', 'your-textdomain'),
        'add_new_item' => __('Add New Book', 'your-textdomain'),
        'new_item' => __('New Book', 'your-textdomain'),
        'edit_item' => __('Edit Book', 'your-textdomain'),
        'view_item' => __('View Book', 'your-textdomain'),
        'all_items' => __('All Books', 'your-textdomain'),
        'search_items' => __('Search Books', 'your-textdomain'),
        'parent_item_colon' => __('Parent Books:', 'your-textdomain'),
        'not_found' => __('No books found.', 'your-textdomain'),
        'not_found_in_trash' => __('No books found in Trash.', 'your-textdomain'),
        'featured_image' => _x('Book Cover Image', 'Overrides the “Featured Image” phrase', 'your-textdomain'),
        'set_featured_image' => _x('Set cover image', 'Overrides the “Set featured image” phrase', 'your-textdomain'),
        'remove_featured_image' => _x('Remove cover image', 'Overrides the “Remove featured image” phrase', 'your-textdomain'),
        'use_featured_image' => _x('Use as cover image', 'Overrides the “Use as featured image” phrase', 'your-textdomain'),
        'archives' => _x('Book archives', 'The post type archive label used in nav menus', 'your-textdomain'),
        'insert_into_item' => _x('Insert into book', 'Overrides the “Insert into post”/“Insert into page” phrase', 'your-textdomain'),
        'uploaded_to_this_item' => _x('Uploaded to this book', 'Overrides the “Uploaded to this post”/“Uploaded to this page” phrase', 'your-textdomain'),
        'filter_items_list' => _x('Filter books list', 'Screen reader text for the filter links', 'your-textdomain'),
        'items_list_navigation' => _x('Books list navigation', 'Screen reader text for the pagination', 'your-textdomain'),
        'items_list' => _x('Books list', 'Screen reader text for the item list', 'your-textdomain'),
    );

    // Arguments for the post type
    $args = array(
        'labels' => $labels,
        'public' => true,
        'publicly_queryable' => true,
        'show_ui' => true,
        'show_in_menu' => true,
        'query_var' => true,
        'rewrite' => array('slug' => 'book'),
        'capability_type' => 'post',
        'has_archive' => true,
        'hierarchical' => false,
        'menu_position' => null,
        'supports' => array('title', 'editor', 'author', 'thumbnail', 'excerpt', 'comments'),
    );

    // Register the post type
    register_post_type('book', $args);
}
?>
```
This code creates a new custom post type named "Books". This $labels array is part of the WordPress custom post type registration process. It defines various text labels used in the WordPress admin interface for your custom post type. Here's a breakdown of each line:

#### 'name' => _x('Books', 'Post type general name', 'your-textdomain'):
- `'name'': The general, plural name for the post type, usually capitalized.
- _x(): A function used for localization and context-specific translation. It allows for the translation of the string 'Books' based on the context provided.
- 'Books': The plural name of the post type as it will appear in the admin menu and other places.
- 'Post type general name': Context for translators to understand where this string is used.
- 'your-textdomain': The text domain for translation, which should match your theme or plugin's text domain.

#### 'singular_name' => _x('Book', 'Post type singular name', 'your-textdomain'):
- 'singular_name': The singular name of the post type.
- 'Book': Singular form of the post type name.

#### 'menu_name' => _x('Books', 'Admin Menu text', 'your-textdomain'):
- 'menu_name': The name shown in the admin menu.
- 'Books': The text to be used in the menu.

#### 'name_admin_bar' => _x('Book', 'Add New on Toolbar', 'your-textdomain'):
- 'name_admin_bar': The name given for the "Add New" dropdown on admin bar.
- 'Book': Text to show in the admin bar when adding a new post of this type.

#### 'add_new' => __('Add New', 'your-textdomain'):
- 'add_new': The label for adding a new singular item.
- __('Add New', 'your-textdomain'): Translates 'Add New' in the given text domain.

#### 'add_new_item' => __('Add New Book', 'your-textdomain'):
- 'add_new_item': The label for adding a new item of this post type.

#### 'new_item' => __('New Book', 'your-textdomain'):
- 'new_item': The text for the new item label.

#### 'edit_item' => __('Edit Book', 'your-textdomain'):
- 'edit_item': The label for editing an item of this post type.

#### 'view_item' => __('View Book', 'your-textdomain'):
- 'view_item': The label for viewing the post type item.

#### 'all_items' => __('All Books', 'your-textdomain'):
- 'all_items': Label to list all items of this post type.

#### 'search_items' => __('Search Books', 'your-textdomain'):
- 'search_items': Label for searching the items.

#### 'parent_item_colon' => __('Parent Books:', 'your-textdomain'):
- 'parent_item_colon': The label for parent items, used for hierarchical post types.

#### 'not_found' => __('No books found.', 'your-textdomain'):
- 'not_found': Message shown when no items are found.

#### 'not_found_in_trash' => __('No books found in Trash.', 'your-textdomain'):
- 'not_found_in_trash': Message for when no items are found in Trash.

#### 'featured_image' => _x('Book Cover Image', 'Overrides the “Featured Image” phrase', 'your-textdomain'):
= 'featured_image': Label for the featured image meta box.

#### 'set_featured_image' => _x('Set cover image', 'Overrides the “Set featured image” phrase', 'your-textdomain'):
- 'set_featured_image': Label for setting the featured image.

#### 'remove_featured_image' => _x('Remove cover image', 'Overrides the “Remove featured image” phrase', 'your-textdomain'):
- 'remove_featured_image': Label for removing the featured image.

#### 'use_featured_image' => _x('Use as cover image', 'Overrides the “Use as featured image” phrase', 'your-textdomain'):
- 'use_featured_image': Label for using an image as the featured image.

#### 'archives' => _x('Book archives', 'The post type archive label used in nav menus', 'your-textdomain'):
- 'archives': Label for post type archives.

#### 'insert_into_item' => _x('Insert into book', 'Overrides the “Insert into post”/“Insert into page” phrase', 'your-textdomain'):
- 'insert_into_item': Label for inserting media into this post type.

#### 'uploaded_to_this_item' => _x('Uploaded to this book', 'Overrides the “Uploaded to this post”/“Uploaded to this page” phrase', 'your-textdomain'):
- 'uploaded_to_this_item': Label for media uploaded to this post type.

#### 'filter_items_list' => _x('Filter books list', 'Screen reader text for the filter links', 'your-textdomain'):
- 'filter_items_list': Label for filtering the list of items.

#### 'items_list_navigation' => _x('Books list navigation', 'Screen reader text for the pagination', 'your-textdomain'):
- 'items_list_navigation': Label for navigation between list items.

#### 'items_list' => _x('Books list', 'Screen reader text for the item list', 'your-textdomain'):
- 'items_list': Label for the items list.


### Step 3: Customizing Your Custom Post Type
You can further customize the custom post type by tweaking the $args array. For example, you can change its visibility, support for certain features like thumbnails, and more.

```
function create_custom_post_type() {
    // ... [Previous code for $labels]

    // Customizing the $args array
    $args = array(
        // Visibility and Access
        'public' => true, // Makes it public
        'show_ui' => true, // Displays the UI in admin
        'show_in_nav_menus' => true, // Available in navigation menus
        'show_in_menu' => true, // Visible in the admin menu
        'show_in_admin_bar' => true, // Visible in the WordPress admin bar

        // Permalinks and Rewrites
        'rewrite' => array('slug' => 'book'), // Custom slug for permalinks

        // Support for features
        'supports' => array('title', 'editor', 'author', 'thumbnail', 'excerpt', 'comments'),

        // Hierarchical structure
        'hierarchical' => false, // False like posts, true like pages

        // Archive page settings
        'has_archive' => true, // Enables archive pages

        // Capability type
        'capability_type' => 'post', // Uses permissions of 'posts'

        // Menu position and icon
        'menu_position' => 5, // Position in admin menu
        'menu_icon' => 'dashicons-book-alt', // Icon for the menu item (a book icon in this case)

        // ... [Other arguments you might want to customize]
    );

    // Register the post type
    register_post_type('book', $args);
}
add_action('init', 'create_custom_post_type');

```
### Explanation:
#### Visibility and Access:
- 'public', 'show_ui', 'show_in_nav_menus', 'show_in_menu', 'show_in_admin_bar' are all set to true to make the post type fully accessible and visible in the admin area and menus.

#### Permalinks and Rewrites:
The 'rewrite' argument is used to define the URL slug for your custom post type. Here, it's set to 'book', meaning the URL for this post type will be yourdomain.com/book.

#### Support for Features:
The 'supports' array lists all the features this post type supports, like the title, editor, author, thumbnails (featured images), excerpts, and comments.

#### Hierarchical Structure:
'hierarchical' is set to false, meaning this post type will behave like standard posts. If set to true, it would behave like pages, allowing for parent-child relationships.

#### Archive Page Settings:
'has_archive' is true, enabling archive pages for this post type.

#### Capability Type:
'capability_type' is set to 'post', which means it will use the same user permissions as standard posts.

#### Menu Position and Icon:
'menu_position' and 'menu_icon' customize where the post type appears in the admin menu and what icon it uses.
