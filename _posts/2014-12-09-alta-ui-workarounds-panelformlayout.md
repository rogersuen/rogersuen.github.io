---
title: Alta UI Workarounds - panelFormLayout
tags: ["Alta UI", "ADF Skin", "ADF 12.1.3", ADF, "ADFSamples", "AltaUIWorkarounds"]
excerpt: 
  When working with Alta UI skin, we can achieve better vertical alignment and distribution by unifying vertical-align, line-height and the vertical paddings of the panelFormLayout component. 
---
After days of hard working to fix issues of vertical alignment and distribution between different components in a complex form, I realized I need a unified solution, instead of ad-hoc, case by case pixel tuning for Oracle Alta UI skin. This is the second post on Alta UI Workarounds, but it would be first in the table of content of the series. 

Considering the massive size of the skin file, the posts and workarounds are by no means exaustive, just some notes, good ideas or bad ideas. We will start from some basic rules and then the `panelFormLayout` component. 

To align inline-level boxes, the basic idea here is to align them vertically using `"vertical-align: middle"`, then make sure the height of all inline-level boxes is as described below.

For replaced inline elements, inline-block elements, and inline-table elements, the height of the inline-level box is their margin box; for non-replaced inline elements, it's their `"line-height"`. Thus, we use a global `"line-height"`; setup a target line box height, then try best to make the height of margin boxes of all inline elements other than non-replaced inline elements to be the target line box height.

To simplify alignment, as long as possible, try keep vertical padding and margin distributed evenly to the top and bottom side, so that they work for any `"vertical-align"` value in the same way. For horizontal padding and margin, distribute them evenly to the left and right side, so that they work for both `ltr` and `rtl` direction.

Ok, here comes the basic rules I'm using:

```css
.XTargetLineBoxHeight:alias {
  height: 28px;
}

/* The target line-height in pixel for the default font-size (12px) is 18px 
 * (leaving 10px for the top and bottom paddings), thus set line-height here
 * to unitless value of 1.5 (18/12 = 1.5). 
 */
.XDefaultLineHeight:alias {
  line-height: 1.5;
}

/* margin box height = border box height + margin-top + margin-bottom */
.XDefaultFormControlHeightMarginBox:alias {
  -tr-rule-ref: selector(".XTargetLineBoxHeight:alias");
}

.XDefaultFormControlHeightBorderBox:alias {
  -tr-rule-ref: selector(".XTargetLineBoxHeight:alias");
}

.XFormControlVerticalMargins:alias {
  margin-top: 0;
  margin-bottom: 0;
}

/* TODO: revisit this setting */
.XFormCellVerticalPaddings:alias {
  padding-top: 2px;
  padding-bottom: 2px;
}
```

Now, apply the basic rules to `panelFormLayout`:

```css
/* in panelFormLayout, use consistent line-height for better vertical
 * alignment and vertical distribution. 
 */
af|panelFormLayout {
  -tr-rule-ref: selector(".XDefaultLineHeight:alias");
}

/* use the same padding for the top and bottom of the form cell to simplify
 * the logic of vertical spacing between cells.
 */
.AFPanelFormLayoutContentCell,
af|panelFormLayout::content-cell,
af|panelFormLayout::label-cell,
af|panelLabelAndMessage::content-cell.AFPanelFormLayoutContentCell {
  -tr-rule-ref: selector(".XFormCellVerticalPaddings:alias");
}
```

Then, we need style the `label` in the `panelFormLayout` component.

```css
/* make label an fixed-height inline-block element, so that it aligns with
 * other components easier, either for start or top labelAlignment, or 
 * for top, middle or bottom vertical-align.
 */
.XLabel:alias {
  box-sizing: border-box;
  display: inline-block;
  vertical-align: middle;
  padding: 5px 0;
  -tr-rule-ref: selector(".XDefaultLineHeight:alias");    
  -tr-rule-ref: selector(".XTargetLineBoxHeight:alias");
}

af|panelFormLayout label {
  -tr-rule-ref: selector(".XLabel:alias");
}
```

Now, the bad news first, we still have to play that tedious pixel game - fixing `line-height` and `padding` for many form control components, especially agent specific styles. The good news is, this time, we have clearly defined simple rules. We can do it in a consistent way. For example, to fix the styles of the `inputText` component, I have the following:

```css
/* basic styling of inputText */
.XInputText:alias {
  box-sizing: border-box;
  display: inline-block;
  vertical-align: middle;
  margin: 0;
  padding: 4px 6px;
  border-width: 1px;
  -tr-rule-ref: selector(".XDefaultLineHeight:alias");    
  -tr-rule-ref: selector(".XTargetLineBoxHeight:alias");    
}

af|inputText::content {
  -tr-rule-ref: selector(".XInputText:alias");    
}
```

The really dirty part is actually to remove styles from the inherited skin. I'm trying to do it in this way:

```css
af|inputText::label {
  -tr-inhibit: padding-top;
}
af|inputText::label:rtl {
  -tr-inhibit: padding-top;
}
```

and in this way:

```css
/* reset native radio and checkbox input elements, so that they line up 
 * with their labels properly. 
 */
.XSelectOneRadioNativeInput:alias {
  position: static;
  vertical-align: middle;
  margin: 0;
  padding: 0;    
}

af|selectBooleanRadio::native-input,
af|selectOneRadio::native-input,
af|selectBooleanCheckbox::native-input,
af|selectManyCheckbox::native-input,
af|selectBooleanRadio::native-input:rtl,
af|selectOneRadio::native-input:rtl,
af|selectBooleanCheckbox::native-input:rtl,
af|selectManyCheckbox::native-input:rtl {
    -tr-rule-ref: selector(".XSelectOneRadioNativeInput:alias");
}

@agent webkit, gecko, ie {
  af|selectOneRadio::native-input,
  af|selectBooleanRadio::native-input,
  af|selectBooleanCheckbox::native-input,
  af|selectManyCheckbox::native-input,
  af|selectOneRadio::native-input:rtl,
  af|selectBooleanRadio::native-input:rtl,
  af|selectBooleanCheckbox::native-input:rtl,
  af|selectManyCheckbox::native-input:rtl {
    -tr-rule-ref: selector(".XSelectOneRadioNativeInput:alias");
  }
}
```

Here's the display result of the sample page with Alta UI:

![Image: panelFormLayout with Alta UI]({{ site.baseurl }}{{ page.id }}/panelformlayout-alta.png)

This is the result from the extended UI skin:

![Image: panelFormLayout with Alta UI Workarounds]({{ site.baseurl }}{{ page.id }}/panelformlayout-alta-workarounds.png)

The workaround looks better. It perfectly lines up components and labels horizontally, distributes components vertically, and it works in a consistent way for different components and layouts.

**Sample Applications:**

* [Source Code](https://github.com/adfsamples/AltaUIWorkarounds)
* [Download ZIP](https://github.com/adfsamples/AltaUIWorkarounds/archive/master.zip)

**Environment:**

* Oracle Alta UI
* JDeveloper 12.1.3.0.0 Build JDEVADF_12.1.3.0.0_GENERIC_140521.1008.S
* Safari Version 8.0
* Mac OS X Version 10.10
