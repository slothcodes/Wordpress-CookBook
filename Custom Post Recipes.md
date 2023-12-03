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

### Step 2: Adding the Custom Post Type to Your Site
After adding this code to your functions.php file, you need to visit your WordPress site. You should now see a new menu item in the WordPress admin for your custom post type.

### Step 3: Customizing Your Custom Post Type
You can further customize the custom post type by tweaking the $args array. For example, you can change its visibility, support for certain features like thumbnails, and more.
