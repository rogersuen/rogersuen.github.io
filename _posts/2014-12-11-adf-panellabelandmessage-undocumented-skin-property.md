---
title: ADF - panelLabelAndMessage's undocumented skin property
tags: [ADF Skin, ADF 12.1.3, ADF]
excerpt: 
---

When working on the skinning of the `panelLabelAndMessage` component, you may have noticed a special skinning technique used for this component. For example, from a simple snippet below: 

```jsp
<af:panelLabelAndMessage label="Label 1" id="plam1">
  <af:outputText value="outputText in panelLabelAndMessage" id="ot1"/>
</af:panelLabelAndMessage>
```

An HTML output is rendered with something like this:

```html
<td valign="top" style="padding-left:14px" class="AFPanelFormLayoutContentCell af_panelLabelAndMessage_content-cell">outputText in panelLabelAndMessage</td>
```

I'm talking about the `"padding-left:14px"` inline style which actually comes from the skin (Alta UI skin for this case). For some reason, a special undocumented ADF Skin property is used to give the hint to the renderkit to generate this inline style. You can find it in the skin file of for example **Alta UI** (or **Skyros**):

`alta-v1-desktop.css`:

```css

/* For non-input content, we need to render some padding on the panelLabelAndMessage
 * content-cell to align the leading edge of the content with the input content. */
af|panelLabelAndMessage {
  -tr-output-content-padding: 14px;
}

```

Here's the Properties window:

![Image: af|panelLabelAndMessage - Properties]({{ site.baseurl }}{{ page.id }}/af_panellabelandmessage_properties.png)

Luckily, we can customize the value in the extended skin file, or simply remove it with the `-tr-inhibit` ADF Skin property:

```css
af|panelLabelAndMessage {
  -tr-inhibit: -tr-output-content-padding;
}
```

**Resources:**

* [Properties in the ADF Skinning Framework](https://docs.oracle.com/middleware/1213/skineditor/develop-skins/adf-skin-selectors.htm#ADFSG544)
* [Oracle Fusion Middleware Tag Reference for Oracle ADF Faces Skin Selectors
12c (12.1.3)](https://docs.oracle.com/middleware/1213/adf/tag-reference-skin-selectors/toc.htm)