---
title: datalist
slug: Web/HTML/Element/datalist
tags:
  - HTML5
  - datalist
translation_of: Web/HTML/Element/datalist
original_slug: Web/HTML/Elemento/datalist
---

El **elemento HTML `<datalist>`** contiene un conjunto de elementos {{HTMLElement("option")}} que representan los valores disponibles para otros controles.

La fuente de este ejemplo interactivo se almacena en un repositorio de GitHub. Si desea contribuir al proyecto de ejemplos interactivos, copia <https://github.com/mdn/interactive-examples> y envíenos una solicitud de extracción.

<table class="properties">
  <tbody>
    <tr>
      <th scope="row">
        <a href="/en-US/docs/HTML/Content_categories"
          >Contenido de las categorías</a
        >
      </th>
      <td>
        <a href="/en-US/docs/HTML/Content_categories#Flow_content"
          >Contenido de flujo</a
        >,
        <a href="/en-US/docs/HTML/Content_categories#Phrasing_content"
          >contenido de fraseo</a
        >.
      </td>
    </tr>
    <tr>
      <th scope="row">Contenido permitido</th>
      <td>
        O bien
        <a href="/en-US/docs/HTML/Content_categories#Phrasing_content"
          >contenido de fraseo</a
        >
        o, cero o más elementos {{HTMLElement("option")}}.
      </td>
    </tr>
    <tr>
      <th scope="row">Omisión de etiqueta</th>
      <td>{{no_tag_omission}}</td>
    </tr>
    <tr>
      <th scope="row">Elementos padre permitidos</th>
      <td>
        Cualquier elemento que acepte
        <a href="/en-US/docs/HTML/Content_categories#Phrasing_content"
          >contenido de fraseo</a
        >.
      </td>
    </tr>
    <tr>
      <th scope="row">Roles ARIA permitidos</th>
      <td>Ninguno</td>
    </tr>
    <tr>
      <th scope="row">Interfaz DOM</th>
      <td>{{domxref("HTMLDataListElement")}}</td>
    </tr>
  </tbody>
</table>

## Atributos

Este elemento no tiene otros atributos mas que los [atributos globales](/es/docs/HTML/Global_attributes), comunes a todos los elementos.

## Ejemplos

```html
<label>Choose a browser from this list:
<input list="browsers" name="myBrowser" /></label>
<datalist id="browsers">
  <option value="Chrome">
  <option value="Firefox">
  <option value="Internet Explorer">
  <option value="Opera">
  <option value="Safari">
  <option value="Microsoft Edge">
</datalist>
```

### Resultado

{{EmbedLiveSample("Examples")}}

## Especificaciones

| Especificación                                                                                               | Estado                           | Comentario |
| ------------------------------------------------------------------------------------------------------------ | -------------------------------- | ---------- |
| {{SpecName('HTML WHATWG', 'forms.html#the-datalist-element', '&lt;datalist&gt;')}} | {{Spec2('HTML WHATWG')}} |            |
| {{SpecName('HTML5 W3C', 'forms.html#the-datalist-element', '&lt;datalist&gt;')}} | {{Spec2('HTML5 W3C')}}     |            |

## Compatibilidad con los distintos navegadores

{{Compat("html.elements.datalist")}}

## sección de relleno

Incluya este polyfill para proporcionar soporte para navegadores antiguos y actualmente incompatibles:
[datalist-polyfill](https://github.com/mfranzke/datalist-polyfill)

## Ver también

- El elemento {{HTMLElement("input")}}, y más especificamente este atributo {{htmlattrxref("list", "input")}};
- El elemento {{HTMLElement("option")}}.

{{HTMLSidebar}}