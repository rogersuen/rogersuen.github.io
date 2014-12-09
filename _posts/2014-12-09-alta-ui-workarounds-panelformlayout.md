---
title: Alta UI Workarounds - panelFormLayout
tags: ["Alta UI", "ADF Skin", "ADF 12.1.3", ADF, "ADFSamples", "AltaUIWorkarounds"]
excerpt: 
  When working with Alta UI skin, we can achieve better vertical alignment and distribution by unifying line-height and the vertical paddings of the panelFormLayout component. 
---
After days of hard working to fix issues of vertical alignment and distribution between different components in a complex form, I realized I need a unified solution, instead of ad-hoc, case by case pixel tuning for Oracle Alta UI skin. This is the second post on Alta UI Workarounds, but it would be first in the table of content of the series. We start from the `panelFormLayout` component.

Here's the basic idea: unify the `line-height` in the `panelFormLayout` component; distribute the vertical padding evenly to the top and bottom of the `content-cell` and `label-cell`, such that components can line up horizontally and distribute vertically better and easier.

{% highlight css %}
/* in panelFormLayout, use consistent line-height for better vertical
 * alignment and vertical distribution. 
 */
af|panelFormLayout {
  line-height: 28px;
}

/* use the same padding for the top and bottom of the content-cell to simplify
 * the logic of vertical spacing between cells.
 */
af|panelFormLayout::content-cell,
.AFPanelFormLayoutContentCell {
  padding-top: 3px;
  padding-bottom: 3px;
}

/* ensure panelLabelAndMessage in a panelFormLayout uses the same padding */
af|panelLabelAndMessage::content-cell.AFPanelFormLayoutContentCell {
  padding-top: 3px;
  padding-bottom: 3px;
}

/* label-cell needs the same paddings as the content-cell  */
af|panelFormLayout::label-cell {
  padding-top: 3px;
  padding-bottom: 3px;
  vertical-align: top;  /* more commonly expected */
}
{% endhighlight %}

Then, we need style the `label` in the `panelFormLayout` component.

{% highlight css %}
/* uses line-height and vertical-align (instead of padding) to line up 
 * the label-cell with the corresponding content-cell. this approach 
 * is supposed to work better in most cases, and provides the consistent
 * styling for top, middle and bottom vertical-align of the label cell.
 */
af|panelFormLayout::label-cell label {
  box-sizing: border-box;
  display: inline-block;
  vertical-align: middle;
  line-height: 28px;
  padding-top: 0;
  padding-bottom: 0;
  white-space: nowrap; /* more commonly expected */
}
{% endhighlight %}

Now, the bad news first, we still have to play that tedious pixel game - fixing `line-height` and `padding` for the most of form control components, if it's not all. The good news is, this time, we have clearly defined simple rules. We can do it in a consistent way. For example, to fix the styles of the `selectOneRadio` component, we need:

{% highlight css %}
/* uses the same line-height to ensure consistent vertical distribution even
 * when grouped with other components.
 */
af|selectOneRadio::content {
  line-height: 28px;
}

@agent webkit {
  af|selectOneRadio af|panelFormLayout::label-cell {
    padding-top: 3px; /* padding-top: 7px; (10392) */
  }    
}
{% endhighlight %}

Here's the display result of the sample page with Alta UI:

![Image: panelFormLayout with Alta UI]({{ site.baseurl }}{{ page.id }}/panelformlayout-alta.png)

This is the result from the extended UI skin:

![Image: panelFormLayout with Alta UI Workarounds]({{ site.baseurl }}{{ page.id }}/panelformlayout-alta-workarounds.png)

The workaround looks better. It perfectly lines up components and labels horizontally, distribute components vertically, and it works in a consistent way for different components and layouts.

**Sample Applications:**

* [Source Code](https://github.com/adfsamples/AltaUIWorkarounds)
* [Download ZIP](https://github.com/adfsamples/AltaUIWorkarounds/archive/master.zip)

**Environment:**

* Oracle Alta UI
* JDeveloper 12.1.3.0.0 Build JDEVADF_12.1.3.0.0_GENERIC_140521.1008.S
* Safari Version 8.0
* Mac OS X Version 10.10
