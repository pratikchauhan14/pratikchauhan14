# 🌀 HubL Cheatsheet

A structured reference for common HubL (HubSpot Markup Language) snippets.

---

## 1. Format Date

```twig
{% if html_lang == 'de' %}
  {{ details.date_field|plus_time(1, "days")|format_datetime("dd. MMMM yyyy") }} 
{% else %}
  {{ details.date_field|plus_time(1, "days")|format_datetime("MMM dd.yyyy") }} 
{% endif %}
```

---

## 2. In Page Edit View Only

```twig
{% if is_in_editor %}
  Your HTML here
{% endif %}
```

---

## 3. Escape HTML

```twig
{{ some_html|striptags }}
```

---

## 4. Include CSS/JS

**Single Includes**
```twig
{{ require_css(get_asset_url("")) }}
{{ require_js(get_asset_url("")) }}
{{ require_js("") }}
```

**Inline JS**
```twig
{% require_js %}
<script>
// your JS here
</script>
{% end_require_js %}
```

**Inline CSS**
```twig
{% require_css %}
<style>
    {% scope_css %}
    /* your CSS here */
    {% end_scope_css %}
</style>
{% end_require_css %}
```

---

## 5. Conditional Widgets / Choices

```twig
{% choice "simple_checking" label='Show/Hide - Simple Checking Section', value='Show', choices='Show, Hide' export_to_template_context=True %}
{% if widget_data.simple_checking.value == 'Show' %} 
    <div> {{ html }} </div>
{% endif %}

{{item2.widgets.event_details.body.speaker}}
```

```twig
{% if is_listing_view %}
    {{ html }}
{% endif %}
```

---

## 6. HubDB Examples

**Show limited rows**
```twig
{% set table2 = hubdb_table_rows(module.hubdbtable_field,"limit=12&market_sector="~module.market_sector~"&market_sub_sector="~module.market_sub_sector) %}
{% if loop.index < 7 %}
{% endif %}
```

**Iterate rows**
```twig
{% for row in hubdb_table_rows(<tableId or name>, <filterQuery>) %}
  the value for row {{ row.hs_id }} is {{ row.<column name> }}
{% endfor %}
```

**Get column options**
```twig
{% set Level = hubdb_table_column(5407254,"i_level").options %}
```

---

## 7. Developer Mode

```twig
&&developerMode=true
```

---

## 8. Lang Switcher

```twig
<div class="header_lag_switcher">
    {% set pages_ = content.translated_content.values()|selectattr('published') %}
    {% set page_ids = [content.id] %}
    {% for i in pages_ %}
        {% do page_ids.append(i.id) %}
    {% endfor %}
    {% set pages = content_by_ids(page_ids) %}
    <div class="lag_switcher {{ "no-lang display:none" if pages_|length == 0 }}">
        <span class="current_lang">{{ html_lang }}</span>
        <ul class="hs-language-switcher" role="menu">
            {% for language in content_by_ids(page_ids) %}
            <li class="{{ "lag__actvie" if language.language.languageTag == html_lang }}">
                <a lang="{{ language.language.languageTag }}" hreflang="{{ language.slug }}" href="{{ language.absolute_url }}">{{ language.language }}</a>
            </li>
            {% endfor %}
        </ul>
    </div>
</div>
```

---

## 9. Accordion / Widget Choices

```twig
{% choice "accordion_section" label='Show/Hide - Accordion Section', value='Show', choices='Show, Hide' export_to_template_context=True %} 
{% if widget_data.accordion_section.value == 'Show' %} 
<div> {{ html }} </div> 
{% endif %}

{% text "button_text" label="Button Text", value="Button Text", export_to_template_context=True %}  
{% text "button_link" label="Button Link", value="#" export_to_template_context=True %} 
{% text "date_field" label="Date Field", export_to_template_context=True %} 
{{ content.widgets.date_field.body.value }}
```

```twig
{% if widget_data.testimonials_section.value == 'Show' %} 
<div> {{ html }} </div> 
{% endif %} 
{% if widget_data.ctastrip_section.value == 'Show' %} 
<div> {{ html }} </div> 
{% endif %} 
{% choice "calculatorn_widget" label='Show/Hide - Calculator Widget', value='Show', choices='Show, Hide' export_to_template_context=True %} 
{% choice "eBook_Widget" label='Show/Hide - eBook Widget', value='Show', choices='Show, Hide' export_to_template_context=True %} 
{% if widget_data.calculatorn_widget.value == 'Show' %} 
    <div> {{ html }} </div> 
{% endif %} 
{% if widget_data.eBook_Widget.value == 'Show' %} 
    <div> {{ html }} </div> 
{% endif %}
```

---

## 10. Video Autoplay

```twig
<video autoplay loop playsinline muted> 
  <source src="{{ module.video_url }}" type="video/mp4"> 
</video>
```
