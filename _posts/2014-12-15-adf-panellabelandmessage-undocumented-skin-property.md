---
title: ADF - panelLabelAndMessage's undocumented skin property
tags: [ADF Skin, ADF 12.1.3, ADF]
excerpt: introducing an undocumented ADF Skin property "-tr-output-content-padding" used by the panelLabelAndMessage component to generate an HTML inline style for non-input content， adding leading padding, so that they align properly with input content.
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

**Chinese Summary:**

ADF 在 组件 `panelLabelAndMessage` 的皮肤中，使用了一个没有正式文档的 ADF 皮肤属性 `"-tr-output-content-padding"` 用以为 `panelLabelAndMessage` 的非输入型内容生成 HTML 的内联样式，添加前置留白，从而方便其与输入型的内容对齐。

**Resources:**

* [Properties in the ADF Skinning Framework](https://docs.oracle.com/middleware/1213/skineditor/develop-skins/adf-skin-selectors.htm#ADFSG544)
* [Oracle Fusion Middleware Tag Reference for Oracle ADF Faces Skin Selectors
12c (12.1.3)](https://docs.oracle.com/middleware/1213/adf/tag-reference-skin-selectors/toc.htm)