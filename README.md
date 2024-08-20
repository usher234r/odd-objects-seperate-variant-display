# seperate-variant-display

Purpose
The purpose of this project is to set up a separate display for product variants on the Shopify store
Store Theme: Minimog - OS 2.0 version 4.0.0

Technologies Used: Liquid/HTML/CSS

Project Files: main-collection-product-grid.liquid, card-variant.liquid
Implementation Steps:
Locate the Code:

Open the main-collection-product-grid.liquid file.
Find the line that contains:
liquid
Copy code
for product in collection.products
Add Variant Option Indexing:

Directly after the line above, insert the following code:

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
Replace Product Card Rendering Logic:

Locate the following block of code in the same file:

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
Replace it with the following code to handle variant-specific display logic:

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

This documentation provides a clear and structured explanation for implementing the separate variant display set up in the Minimog - OS 2.0 version 4.0.0 Shopify store theme. If any further details or clarifications are needed, feel free to reach out!
