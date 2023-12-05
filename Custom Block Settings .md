# Custom Gutenberg Block Styling Cheat Sheet

## Introduction
This cheat sheet serves as a guide for WordPress developers, especially focusing on adding controls and options to the block settings panel in Gutenberg. It aims to streamline the process of custom block development by providing concise yet comprehensive code examples and explanations.

## Table of Contents
- [Text Controls](#text-controls)
- [Color Picker](#color-picker)
- [Media Upload](#media-upload)
- [Range Slider](#range-slider)
- [Dropdown Menu](#dropdown-menu)
- [Toggle Control](#toggle-control)
- [Custom Controls](#custom-controls)
- [Applying CSS Styles](#applying-css-styles)
- [Best Practices](#best-practices)

## Text Controls
Text controls are essential for allowing users to input text within Gutenberg blocks.

### Basic Text Control
```
// Basic text control in the block's edit function
const { TextControl } = wp.components;

<TextControl
    label="Simple Text Input"
    value={ text }
    onChange={ ( newText ) => setAttributes({ text: newText }) }
/>
```
### Rich Text Control
```
// Rich text control for more complex text editing
const { RichText } = wp.blockEditor;

<RichText
    tagName="p" // The tag here could be any HTML tag.
    value={ richText }
    onChange={ ( newContent ) => setAttributes({ richText: newContent }) }
/>
```
## Color Picker
Color pickers provide an intuitive interface for selecting colors.

### Color Palette
```
// Using ColorPalette for color selection
const { ColorPalette } = wp.components;

<ColorPalette
    value={ color }
    onChange={ ( newColor ) => setAttributes({ color: newColor }) }
/>
```
### Custom Color Picker
```
// Custom color picker with more control
const { PanelColorSettings } = wp.blockEditor;

<PanelColorSettings
    title="Color Settings"
    colorSettings={[
        {
            value: backgroundColor,
            onChange: setBackgroundColor,
            label: 'Background Color',
        },
        {
            value: textColor,
            onChange: setTextColor,
            label: 'Text Color',
        },
    ]}
/>
```

## Media Upload
For integrating media upload functionalities into blocks.
### Basic Media Upload
```
// Basic MediaUpload for images
const { MediaUpload, MediaUploadCheck } = wp.blockEditor;
const { Button } = wp.components;

<MediaUploadCheck>
    <MediaUpload
        onSelect={ media => setAttributes({ image: media.url })}
        allowedTypes={['image']}
        render={ ({ open }) => (
            <Button onClick={ open }>Choose Image</Button>
        )}
    />
</MediaUploadCheck>
```
### Advanced Media Options
```
// Advanced MediaUpload with custom render
const { MediaUpload, MediaUploadCheck } = wp.blockEditor;
const { Button } = wp.components;

<MediaUploadCheck>
    <MediaUpload
        onSelect={ media => setAttributes({ video: media.url })}
        allowedTypes={['video']}
        render={({ open }) => (
            <div>
                <Button onClick={ open }>Choose Video</Button>
                <Button onClick={ () => setAttributes({ video: null }) }>
                    Remove Video
                </Button>
            </div>
        )}
    />
</MediaUploadCheck>
```
## Range Slider
Adding interactive range sliders to blocks.
### Basic Range Slider
```
// Basic RangeControl for numerical values
const { RangeControl } = wp.components;

<RangeControl
    label="Column width"
    value={ columnWidth }
    onChange={ ( newWidth ) => setAttributes({ columnWidth: newWidth })}
    min={ 0 }
    max={ 100 }
/>
```
### Custom Range Slider
```
// Custom RangeControl with additional settings
const { RangeControl } = wp.components;

<RangeControl
    beforeIcon="arrow-left-alt2"
    afterIcon="arrow-right-alt2"
    label="Custom Range Slider"
    value={ customRange }
    onChange={ ( newRange ) => setAttributes({ customRange: newRange })}
    min={ 10 }
    max={ 60 }
    step={ 5 }
/>
```

## Dropdown Menu
Dropdown menus for selecting from a list of options.
### Basic Dropdown
```
// Basic SelectControl for dropdown options
const { SelectControl } = wp.components;

<SelectControl
    label="Select a size"
    value={ size }
    options={[
        { label: 'Small', value: 'small' },
        { label: 'Medium', value: 'medium' },
        { label: 'Large', value: 'large' },
    ]}
    onChange={ ( newSize ) => setAttributes({ size: newSize }) }
/>
```
### Custom Dropdown Options
```
// Custom SelectControl with grouped options
const { SelectControl } = wp.components;

<SelectControl
    label="Choose a font"
    value={ font }
    options={[
        { label: 'Sans Serif', value: 'sans-serif' },
        { label: 'Serif', value: 'serif' },
        { label: 'Monospace', value: 'monospace', disabled: true }, // Disabled option
    ]}
    onChange={ ( newFont ) => setAttributes({ font: newFont }) }
/>
```

## Toggle Control
Toggle controls for binary choices.
### Basic Toggle
```
// Basic ToggleControl
const { ToggleControl } = wp.components;

<ToggleControl
    label="Show title"
    checked={ showTitle }
    onChange={ ( newState ) => setAttributes({ showTitle: newState }) }
/>
```
### Styled Toggle
```
// ToggleControl with custom styling
const { ToggleControl } = wp.components;

<ToggleControl
    label="Enable dark mode"
    checked={ darkMode }
    onChange={ ( newMode ) => setAttributes({ darkMode: newMode }) }
    className="custom-dark-mode-toggle"
/>
```

## Custom Controls
For when built-in controls don't meet your specific needs.
### Creating a Custom Control
```
// Example of a custom control component
const CustomControl = ({ value, onChange }) => (
    <div className="my-custom-control">
        <input type="text" value={ value } onChange={ (e) => onChange(e.target.value) } />
        <button onClick={ () => onChange('') }>Clear</button>
    </div>
);

// Usage in your block
<CustomControl
    value={ customValue }
    onChange={ ( newValue ) => setAttributes({ customValue: newValue }) }
/>
```
### Integrating Custom Control
```
// Example of integrating the custom control into the block's edit function
<CustomControl
    value={ customText }
    onChange={ ( newCustomText ) => setAttributes({ customText: newCustomText }) }
/>
```
## Applying CSS Styles
Effective methods to apply CSS styles to your blocks.
### Inline Styles
```
// Applying inline styles directly in the block's edit function
<div style={{ backgroundColor: bgColor, color: txtColor }}>
    Custom Styled Block Content
</div>
```
### External Stylesheets
```
// Enqueuing an external stylesheet for your block
function enqueue_block_assets() {
    wp_enqueue_style(
        'my-block-style',
        plugins_url('style.css', __FILE__),
        array(),
        '1.0'
    );
}
add_action('enqueue_block_assets', 'enqueue_block_assets');
```
