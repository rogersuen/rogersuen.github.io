---
title: ADF - The value attribute of SelectMany components
tags: [ADF Faces Component, ADF Faces, ADF, ADFSamples]
excerpt: The value attribute of any SelectMany components, such as selectManyCheckboxes, must be a List or an Array, and the default data type is List.
---

The selection components allow the user to select single and multiple values from a list or group of items. The `value` attribute of `SelectOne` components is straightforward, but what about the `value` of `SelectMany` components?

ADF Faces provides a number of `SelectMany` components. The `selectManyCheckbox` component is one of them which allows the user to select multiple values from a series of checkboxes. In the [Tag Reference](https://docs.oracle.com/middleware/1213/adf/tag-reference-faces/tagdoc/af_selectManyCheckbox.html) of the component, the type of the `value` attibute is `Object`, and the description:

> the value of the component. If the EL binding for the "value" points to a bean property with a getter but no setter, and this is an editable component, the component will be rendered in read-only mode.

Simply the same as it for other components. Not much helpful. After some digging, I found the following code snippet:

```java
// org.apache.myfaces.trinidadinternal.renderkit.core.xhtml.SimpleSelectManyRenderer

// OK, is this a List or an array?
boolean isList = ((modelClass == null) ||
                  modelClass.isAssignableFrom(List.class));
boolean isArray = (modelClass != null) && modelClass.isArray();
// We only support lists and arrays;  if neither, quit.
if (!isList && !isArray)
{
  _throwUnsupportedModelType(context, modelClass, component);
}
```

Clearly, the `value` attribute of `SelectMany` components, must be a `List` or an `Array`, and the default type is `List`.

You can download the sample application - [SelectManyCheckbox.jws](https://github.com/adfsamples/SelectManyCheckbox). This application demonstrates how to handle the `value` attribute and process the selected items of the `selectManyCheckbox` component.

![Image: sample page]({{ site.baseurl }}{{ page.id }}/sample-page.png)

In this sample application. The `value` attribute of one of `selectManyCheckbox` components is designed to be an `Array`; and the other one is left simply as an `Object`.

```java
private Weekday[] days = new Weekday[] { Weekday.Sunday, Weekday.Saturday };
private Object sports;

```

Once the user makes some selections on both components, and submit. You can find the message from the Log Window like this:

```
>>> days: [Lcom.adfsamples.view.beans.SelectManyBean$Weekday;
>>> sports: java.util.ArrayList
```

The data type of the first component (days) is an `Array` as expected, and the second one (sports) is handled as an `ArrayList`, the default data type for the `value` attribute.

**Sample Applications:**

* [Source Code](https://github.com/adfsamples/SelectManyCheckbox)
* [Download ZIP](https://github.com/adfsamples/SelectManyCheckbox/archive/master.zip)

**Environment:**

* Oracle Alta UI
* JDeveloper 12.1.3.0.0 Build JDEVADF_12.1.3.0.0_GENERIC_140521.1008.S
* Safari Version 8.0.2 (10600.2.5)
* Mac OS X Version 10.10.1 (Yosemite)

**Resources**:

[Oracle Fusion Middleware Tag Reference for Oracle ADF Faces
12c (12.1.3)](https://docs.oracle.com/middleware/1213/adf/tag-reference-faces/toc.htm)
