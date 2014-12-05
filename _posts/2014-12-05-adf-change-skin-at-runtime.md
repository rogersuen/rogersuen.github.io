---
title: ADF - Change Skin at Runtime
tags: ["ADF Skin", "ADF Faces", ADF, "ADF Samples"]
excerpt: 
  How to allow end users to change the application's ADF skin at runtime with an EL expression configured for the skin-family property in the trinidad-config.xml file.
---
For a ADF Faces application, you can configure the skin family for the application in the `trinidad-config.xml` file. The `trinidad-config.xml` allows you to define properties using the Expression Language (EL) or static values. The `skin-family` is one of such properties that accepts EL expression as the value, so that you can enable end users to change the application's ADF skin at runtime by exposing a component that allows them to update the value of the `skin-family` property. 

In the [**Sample Application**](https://github.com/adfsamples/ChangeSkinAtRuntime), the `trinidad-config.xml" file is configured like this:

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<trinidad-config xmlns="http://myfaces.apache.org/trinidad/config">
  <skin-family>#{skinSelectorBean.skinFamily}</skin-family>
</trinidad-config>
{% endhighlight %}

Then in the sample page, a `selectOneRadio` component is provided, allowing end users to switch between the **Alta** skin and the **Skyros** skin:

{% highlight jsp %}
<af:selectOneRadio id="sor1" label="Select Skin" autoSubmit="true"
                   valueChangeListener="#{skinSelectorBean.skinFamilyValueChanged}"
                   value="#{skinSelectorBean.skinFamily}">
  <af:selectItem id="si1" label="Alta" value="alta"/>
  <af:selectItem id="si2" label="Skyros" value="skyros"/>
</af:selectOneRadio>
{% endhighlight %}

When the end user chooses the skin from the list, the `skinFamily` property value of the backing bean is updated, and then the `valueChangeListener` method of the backing bean is called. The method updates the whole view in response to the change of the skin, such that the view is rendered with the selected skin. Here, nothing special. The only interesting part is how to update the whole view properly. Please see the comments for the detail.

{% highlight java %}
public void skinFamilyValueChanged(ValueChangeEvent valueChangeEvent) 
throws IOException {
    // ValueChangeEvent is handled in the PROCESS_VALIDATIONS phase. 
    // Calling refreshPage() will skip the UPDATE_MODEL_VALUES phase 
    // which we need to update skinFamily. We have to queue this event 
    // to the INVOKE_APPLICATION phase.
    if (valueChangeEvent.getPhaseId() != PhaseId.INVOKE_APPLICATION) {
        valueChangeEvent.setPhaseId(PhaseId.INVOKE_APPLICATION);
        valueChangeEvent.queue();
        return;
    }

    // refresh the whole view
    refreshView();
}

private void refreshView() {
    FacesContext facesContext = FacesContext.getCurrentInstance();
    String viewId = facesContext.getViewRoot().getViewId();
    ViewHandler viewHandler = facesContext.getApplication().getViewHandler();
    UIViewRoot root = viewHandler.createView(facesContext, viewId);
    root.setViewId(viewId);
    facesContext.setViewRoot(root);
}
{% endhighlight %}

**Chinese Summary:** 

在 `trinidad-config.xml` 配置文件中，可以设置 `skin-family` 属性的值为 EL 表达式。通过相应的组件可以允许用户在运行时更新该表达式的值，从而动态地选择用应用程序的 ADF 皮肤。

**Sample Application:**

* [Source Code](https://github.com/adfsamples/ChangeSkinAtRuntime)
* [Download ZIP](https://github.com/adfsamples/ChangeSkinAtRuntime/archive/master.zip)

**Environment:**

JDeveloper 12.1.3.0.0 Build JDEVADF_12.1.3.0.0_GENERIC_140521.1008.S
