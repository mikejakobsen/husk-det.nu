# Drupal 7.5 PHP

[Empiri](https://docs.google.com/document/d/1PNbqwBBF6D8mm-vTkmNngCNUWwq7gFpK8IBkHV6parE/edit)
[Pluralsight](https://app.pluralsight.com/player?course=drupal7-foundation&author=leo-schuman&name=drupal7-foundation-m2&clip=0&mode=live)

[DB Cheatsheet](../assets/drupal7db.pdf)

[PDF Advices](https://drive.google.com/file/d/0B4OLBRkeTUxARHBWY1dhNW5OUXM/view)

Running Acquia Desktop for now. Php 7.1 complains about MbString.

And Drupal Vm with Vagrant [DrupalVM](https://www.drupalvm.com/)

    #Todo use Laravel Valet to serve Drupal.

## Notes

Note er en enhver content type. Fx. Article, Page.


## Blocks

Region -> Contains zero or more block.

Block -> container for static/dynamic content.

## Modules

Can contain pretty much anything. A 3-party plugin, will probably be a module.

## View

A collection of content, Comments and other data.

*displays as*

[Slides](https://docs.google.com/presentation/d/1idbiQFK8RNGHPdohI86E684nGU0u4wxDIpUfxgXxGkw/edit#slide=id.gca8a7051c_0_0)

## Acquia SSH

mikejakobsen.dev@free-6150.devcloud.hosting.acquia.com

## Render

The content gets renderet to the page. Not just echo.

```php
<?php print render($page['content']); ?>
```

Node is the current used template file.

    node.tpl.php is default.

    node--article.tpl.php

Is fx. used on articles.
