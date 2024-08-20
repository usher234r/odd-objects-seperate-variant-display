# seperate-variant-display

Purpose
The purpose of this project is to set up a separate display for product variants on the Shopify store
Store Theme: **Minimog - OS 2.0 version 4.0.0**

Technologies Used: **Liquid/HTML/CSS**

Project Files: **main-collection-product-grid.liquid**, **card-variant.liquid**

Implementation Steps:
1. Locate the Code:

Open the **main-collection-product-grid.liquid** file.
Find the line that contains:
```liquid
for product in collection.products
```
Add Variant Option Indexing:

Directly after the line above, insert the following code:
```liquid
{%- liquid
   assign option_chosen = "Color" 
   assign option_index = ''
   for option in product.options
       if option_chosen == option
           assign option_index = forloop.index0 
           break
       endif
   endfor
-%}
```
Replace Product Card Rendering Logic:

Locate the following block of code in the same file:
```liquid
<div class="m-product-item m:w-6/12 md:m:w-4/12">
  {% liquid
    case settings.pcard_layout
      when '1'
        render 'product-card-1', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
      when '2'
        render 'product-card-2', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
      when '3'
        render 'product-card-3', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
      when '4'
        render 'product-card-4', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
      when '5'
        render 'product-card-5', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
    endcase
  %}
</div>
```
Replace it with the following code to handle variant-specific display logic:
```liquid
{%- if option_index == '' -%}
<div class="m-product-item m:w-6/12 md:m:w-4/12">
  {% liquid
    case settings.pcard_layout
      when '1'
        render 'product-card-1', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
      when '2'
        render 'product-card-2', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
      when '3'
        render 'product-card-3', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
      when '4'
        render 'product-card-4', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
      when '5'
        render 'product-card-5', product: product, section: section, index: forloop.index, animated: animated, animation_effect: animation_effect
    endcase
  %}
</div>
{%- else -%}
{% assign displayed_values = "" %}
{% for variant in product.variants %}
{%- liquid
  assign variant_option = variant.options[option_index]
  assign valueIsDisplayed = false
  for value in displayed_values
    if value == variant_option
      assign valueIsDisplayed = true
      break
    endif
  endfor
-%}
{% unless valueIsDisplayed %}
    {%- assign variant_option_arr = variant_option | append: ';'  | split: ';' -%}
    {% assign displayed_values = displayed_values | concat: variant_option_arr %}
    <div
    class="m-product-item m:w-6/12 md:m:w-4/12"
    {% if settings.animations_reveal_on_scroll %}
        data-cascade
        style="--animation-order: {{ forloop.index }};"
    {% endif %}
    >
    {% render 'hc-card-variant',
        card_variant: variant,
        card_product: product,
        variant_option: variant_option,
        media_aspect_ratio: section.settings.image_ratio,
        image_shape: section.settings.image_shape,
        show_secondary_image: section.settings.show_secondary_image,
        show_vendor: section.settings.show_vendor,
        show_rating: section.settings.show_rating,
        lazy_load: lazy_load,
        show_quick_add: section.settings.enable_quick_add,
        section_id: section.id
    %}
    </div>
  {% endunless %}
{% endfor %}
{%- endif -%}
```
2. Create a Duplicate of the **product-card-1.liquid** File:

Rename: The new file as **hc-card-variant.liquid**.

Replace Code Snippets:

product: product with product: card_product
variant_id and selected_variant.id with card_variant.id
product.first_available_variant.id or product.selected_or_first_available_variant.id with card_product.first_available_variant.id and card_product.selected_or_first_available_variant.id
preview_image with card_variant.featured_media
second_image with card_product.media[1]
product.url with card_variant.url
product.tags with card_product.tags
product.has_only_default_variant with card_product.has_only_default_variant
product.id with card_product.id
product.metafields.global['minimog_countdown'] with card_product.metafields.global['minimog_countdown']
product.vendor with card_product.vendor
product.url and product.description with card_product.url and card_product.description
{{ product.title }} with {{ card_product.title }} - {{ variant_option }}
Remove:

```liquid
{% if product.has_only_default_variant == false and show_variant_options %}
  {% render 'product-card-option', product: product, show_variant_options: show_variant_options, pcard_alignment: pcard_alignment %}
{% endif %}
```
This documentation provides a clear and structured explanation for implementing the separate variant display set up in the **Minimog - OS 2.0 version 4.0.0** Shopify store theme. If any further details or clarifications are needed, feel free to reach out!
