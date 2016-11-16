# Drupal Theme

Always use child themes, like WordPress.

mytheme.info - Overview and settings. Key - value.
    
    Declare assets. JS/CSS etc.

page.tpl.php - The overall layout. The HTML/PHP Markup.

    HTML. A wrapper for the dynamic content from Drupal.

template.php - Set var and override theme functions.

.tpl files are ways to define layouts, for each type of component. Note, Block etc.


## Tips

Devel to create fake content - Like faker.

## Template.php

Seperating function and queries from the *tpl.php* files.

Queries goes here.

Mostly If blocks, to determine if the variable exist.

## Data structure

Basic PHP stuff.

    $var = variable

    array('ting1', 'ting2');

    array('red' => 'cherries', 'green' => 'apple');

Zero indexed. $array[0]

### Objects

    $robot->color 

    $robot->action()

    ```PHP
      foreach ($stuff as $item) {
      print $item;
      }
    ```

# Theme

- boilerplate.info => provide informations about the theme, like regions, css, settings, js ...
- block-system-main.tpl.php => template to edit the content
- block.tpl.php => template to edit the blocks
- comment.tpl.php => template to edit the comments
- node.tpl.php => template to edit the nodes (in content)
- page.tpl.php => template to edit the page
- template.php => used to modify drupal's default behavior before outputting HTML through 
  the theme
- theme-settings => used to create additional settings in the theme settings page

## Scss


- _base.scss => define sass variables and partials to be included in all .scss files
- _custom.scss => define custom styles for page elements
- _defaults.scss => establishes general rules and browser resets
- _ie6.scss => Fiexes to support IE6
- _ie7.scss => Fiexes to support IE7
- _ie8.scss => Fiexes to support IE8
- _layout.scss => define the layout (positioning) of the theme's major elements
- _mixins.scss => define custom sass functions and mixins for use in other .scss files
- _mobile.scss => define the way the theme addapts on mobile devices
- _print.scss => define the way the theme look like when printed
- style.scss => assembles all partials into a base css to be loaded on every page
- tabs.scss => styles for the admin tabs (from ZEN)
