---
title: Alta UI Workarounds - inputDate's vertical-align in webkit
tags: ["Alta UI", "ADF Skin", "ADF 12.1.3", ADF, "ADFSamples", "AltaUIWorkarounds"]
excerpt: 
  In Alta UI skin, when the inputDate component is grouped with other sibling components in a panelGroupLayout component, and placed in a panelLabelAndMessage component, and then in a panelFormLayout component, it might not be vertically aligned properly in webkit browsers. 
---
Having a lot of fun, and even more pain in the last month, playing with new [**Oracle Alta UI**](http://www.oracle.com/webfolder/ux/middleware/alta/index.html). This post is meant to be the opening of the **Alta UI Workarounds** topic, a collection of short posts on working around Alta UI skin. Check out the `AltaUIWorkarounds` label and the [**Sample Application**](https://github.com/adfsamples/AltaUIWorkarounds)!

Okay, here comes the first case. When the `inputDate` component is grouped with other sibling components in a `panelGroupLayout` component, and placed in a `panelLabelAndMessage` component, and then in a `panelFormLayout` component, it might not be vertically aligned properly in `webkit` browsers. Say, the following code:

{% highlight jsp %}
<af:panelFormLayout id="pfl1">
    <af:panelLabelAndMessage id="plam1" label="Starts">
        <af:panelGroupLayout id="pgl3">
            <af:selectBooleanRadio id="sbr1" text="On" label="On" simple="true"/>
            <af:inputDate id="id1" label="On" simple="true"/>
        </af:panelGroupLayout>
    </af:panelLabelAndMessage>
</af:panelFormLayout>
{% endhighlight %}

The fragment is rendered like this:

![Image: inputDate with Alta UI]({{ site.baseurl }}{{ page.id }}/inputdate-alta.png)

Th problem is in the Alta UI skin file (`alta-v1-desktop.css`, ln:6133):

{% highlight css %}
@agent webkit {
  af|panelLabelAndMessage::content-cell.AFPanelFormLayoutContentCell af|inputDate {
    /*TODO ALTA*/
    vertical-align: 50%;
  }
}
{% endhighlight %}

To fix the issue, simply override the rule in your extended skin file like this:

{% highlight css %}
@agent webkit {
  af|panelLabelAndMessage::content-cell.AFPanelFormLayoutContentCell af|inputDate {
    vertical-align: middle; /* vertical-align: 50%; (ln:6133) */
  }
}
{% endhighlight %}

Now the fragment is rendered as expected:

![Image: inputDate with Alta UI Workarounds]({{ site.baseurl }}{{ page.id }}/inputdate-alta-workarounds.png)

Vertically aligning those input components in a consistent way, say with `vertical-align: middle`, helps to avoid this kind of glitches.

**Sample Applications:**

* [Source Code](https://github.com/adfsamples/AltaUIWorkarounds)
* [Download ZIP](https://github.com/adfsamples/AltaUIWorkarounds/archive/master.zip)

**Environment:**

* Oracle Alta UI
* JDeveloper 12.1.3.0.0 Build JDEVADF_12.1.3.0.0_GENERIC_140521.1008.S
* Safari Version 8.0
* Mac OS X Version 10.10

