---
layout: post
title:  "Turning Sitecore's Variants into Components | Streamline SXA | Part 1"
date:   2018-3-7 11:57
categories: Sitecore SXA
image: https://picsum.photos/400/400/?rnd=31
---

Our sitecore hackathon project attempted to streamline the content editor process of creating a new variant. What we put together was a series of updates to create a single item to organize variant renderings, their variant definitions and their styles. We used the name Components to represent Component based design and development. You can find our work on our [Digital Brewery GitHub Repository](https://github.com/Boyd-B/2018-Digital-Brewery).

We took this further by automating the creation of sass and js files for the component. We created a modal dialog to walk the editor through creating a new component, which would essentially use a branch to create a controller rendering pointing to our new component controller as well as the variant definition and styles item. We also created 2 modal dialogs to create a shortcut to create a js file and sass file based on the name of the rendering. We intended to bridge the create component into creating the js and scss files as extra optional steps, but ran out of time. We intend to keep working and polish off the dialogs the way we intended as we plan to use these in the future.

### Series

There are several steps to completing our task. The first of which is to turn sitecore's variants into components. The next parts of this series will explore the pipelines we created to help automate the creation of components and then finally into the modal dialogs to further streamline the creation of matching sass and js files.

* Part 1: Turning variants into components
* Part 2: Adding pipelines and updating sitecore items.
* Part 3: Adding Modal Dialog wizards
* Part 4: Ideas for improvement

## How it all ties together

What ties everything together is the name of the component you choose. This name is used with every other item. If you look at the razor code for the promo that comes standard with SXA you'll see it is fairly simplistic. However, they have hardcoded the name into the razor file as well as in the css classes. We simply changed the hardcoded value to the rendering name. The rest falls into place.

### Razor view change

{% highlight c# linenos %}
@using Sitecore.Data.Items
@using Sitecore.XA.Foundation.MarkupDecorator.Extensions
@using Sitecore.XA.Foundation.SitecoreExtensions.Extensions
@using Sitecore.XA.Foundation.RenderingVariants.Extensions
@using Sitecore.XA.Foundation.Variants.Abstractions.Fields

@model Sitecore.XA.Foundation.Variants.Abstractions.Models.VariantsRenderingModel
@{
  Item dataSource = Model.DataSourceItem ?? Model.PageItem;
}
@if (dataSource != null || Html.Sxa().IsEdit)
{
  <div @Html.Sxa().Component(Model.Rendering.Name.ToLower().Replace(" ", "-"), Model.Attributes)>
    @if (dataSource == null)
    {
      @Model.MessageIsEmpty
    }
    else
    {
      foreach (BaseVariantField variantField in Model.VariantFields)
      {
        @Html.RenderingVariants().RenderVariant(variantField, dataSource, Model.RenderingWebEditingParams)
      }
    }
  </div>
}
{% endhighlight %}

This creates a simple html structure where the css class of the wrapper is the rendering name.

{% highlight html %}
<div class="call-to-action">
  <div class="content">
    <div class="title">Title</div>
    <div class="text">
      <p>What you see is what you get</p>
	</div>
  </div>
</div>
{% endhighlight %}

### Matching Sass and JS

This is tied to css styles and js using this name. 

{% highlight scss %}
.call-to-action{
  .title{
    font-size: 3rem;
  }
}
{% endhighlight %}

### Update Sitecore Templates

At this point we created a basic controller to render this new view. We created a folder in templates and created a folder, under which we created a new rendering template that inherits from controller rendering. We give this item some standard values to point to our new view. We created a basic rendering folder called our component folder then updated the assignments to have our new component.

*We also created basic a template for a basic data item to be used for each component. This can be optional or duplicated to give a new datasource to each component.*

At this point you can add the component folder to your renderings. Then when you create a new component it will come prepopulated with the new component controller settings.

### Up next

Creating pipelines and updating SXA items to look in the right locations.

Part 1 only creates a component. The next parts of this series will discuss how to bring the components into the presentation item of your site so that you can manage all of your components (renderings), variants and styles in one place.

**Cheers!**

