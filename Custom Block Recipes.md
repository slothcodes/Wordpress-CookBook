# WordPress Custom Block Creation Cheat Sheet

### Step 1: Run Create-Block In Plugin's Folder
```
npx @wordpress/create-block my-block
```
### Step 2: Navigate to Your New Block Directory
```
cd my-block
```
### Step 3: Calibrate For Multi-Block Support
#### Step 3.1: Create 'blocks' subfolder
Create 'blocks' folder to house the individual block folders
#### Step 3.2: Create individual folders for each block
Make a folder for each block in the blocks subfolder
#### Step 3.3: Copy the files into each block folder
- block.json
- edit.js
- editor.scss
- index.js
- save.js
- style.scss
![block folder layout](https://i.stack.imgur.com/zgqDZ.png)

#### Step 3.4: Update the block.json files to give each block a unique name and title
- Update the name and title fields in the block.json file to make them unique identifiers for the blocks. 
- Optional: Update the category, icon and other block meta attributes.

#### Step 3.5: Update the plugins main .php file to register the blocks
```
function create_block_your_plugin_block_init() {
    register_block_type( __DIR__ . '/build/block-1' );
    register_block_type( __DIR__ . '/build/block-2' );
}
add_action( 'init', 'create_block_your_plugin_block_init' );
```

### Step 4: Run The Development Server
```
npm run start
```
### Step 5: Build The Block 
```
npm run build
```
