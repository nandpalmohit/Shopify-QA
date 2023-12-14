# Custom Section Creation Guide in Shopify

## Follow these steps to create a custom section in your Shopify store:

1. Open your `store`, Click on `Online Store`, and Click on `Themes`.

2. Click on the `three dots` and choose `Edit Code`.

3. Now go to `Sections` and create a `new page` with any name.

   **Example:** Create a page with the title `wc-subtitle.liquid`.

4. Add your custom code to the liquid page.

5. Add the following code at the end of the file:

    ```liquid
    {% schema %}
      {
        "name": "WC Subtitle",
        "settings": [
          {
            "id": "wc_subtitle",
            "type": "text",
            "label": "Wc Subtitle"
          }
        ],
        "presets": [
          {
            "name": "WC Subtitle"
          }
        ]
      }
    {% endschema %}

    {% stylesheet %}
    {% endstylesheet %}

    {% javascript %}
    {% endjavascript %}
    ```

    Ensure that you store this code in each section file with your custom code.

6. To get the subtitle dynamically from a component, write the following line:

   ```liquid
   <aside>
   ✅ {{ section.settings.wc_subtitle }}
   </aside>


## Authors

- [@nandpalmohit](https://www.github.com/nandpalmohit)

