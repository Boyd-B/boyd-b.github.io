---
layout: post
title:  "Move renderings to your site's presentation using pipelines | Streamline SXA | Part 1"
date:   2018-3-14 2:28
categories: Sitecore SXA
image: https://picsum.photos/400/400/?rnd=314
---

Our sitecore hackathon project attempted to streamline the content editor process of creating a new variant. What we put together was a series of updates to create a single item to organize variant renderings, their variant definitions and their styles. We used the name Components to represent Component based design and development. You can find our work on our \[Digital Brewery GitHub Repository\](https://github.com/Boyd-B/2018-Digital-Brewery).

### Streamline SXA Series  
There are several steps to completing our task. The first of which is to turn sitecore's variants into components. In this section we moved our renderings, which we're calling components into the presentation section of our site in order to better organize our items.

* Part 1: Turning variants into components
* Part 2: Adding pipelines to change components location
* Part 3: Adding Modal Dialog wizards
* Part 4: Ideas for improvement

## Part 2: Adding pipelines and updating sitecore items  

In the previous post we only created a component, which is a small addition to the variants controller. We are still left with the frustrating process of creating each piece of the component separately in different areas. Our next goal was to organize the pieces that make a component in one easy to find location.

Currently, you must create a new rendering in the layouts section. Then navigate to the site's presentation folder then create a 'Variants' in the 'Variant's Grouping' folder matching the name of the component. Then create a 'Styles' folder matching the component name (Although this is optional, it is encouraged.) in the 'Styles' folder.

### Add new Components item to Presentation
To streamline this process we moved the rendering from layouts into the presentation folder. This allowed us to create the variants and styles under the rendering with a branch. 

In the presentation folder we now have an item called 'Components' and within it, you create an 'Item Component'. Which is just controller rendering pointing to ComponentsController and the Item Action. 

This is actually a branch that also creates the variants and styles items under it matching the name you give the rendering. This is our final tree structure.
<br/>

![Components](/images/streamline/Component-Tree-Structure.png)

### Update Available Renderings to look in teh right spot

Available renderings is set by default to layouts. We need to change the renderings property to look in another place.

{% highlight %}
/sitecore/templates/Foundation/Experience Accelerator/Presentation/Available Renderings/_Renderings List/Data/Renderings
{% endhighlight %}

To make it specifically for components, use the following
{% highlight %}
query:./ancestor-or-self::*[@@templatename='Presentation']/Components
{% endhighlight %}

Otherwise to select from bot hrenderings and components you'll need to remove the source altogether and drill down from the root of teh tree. 
In the fourth part of this series we'll identify this as one of the areas for improvement. We'd like to choose from both locations, but ideally we'd like to move the components into the renderings section with variants and styles under it there. We initially tried this route but I can't recall the issue we ran into that prevented us from sticking with it. So I'd like to revisit it in the future if possible, or if someone can do it for me and tell me, even better!

### Add pipelines to find variants and styles under component
Now that we've moved our variants and styles we need update the pipeline to also look for variants and styles under our new component. This doesn't override any previous functionality so any variants or styles will show up in the current location.

{% highlight c# %}
public class GetComponentVariants
{
    private static readonly Sitecore.Data.ID VariantsTemplateId = new Sitecore.Data.ID("E1A3B30C-77BC-4F6C-A008-D01B3371235D");

    public readonly IContentRepository ContentRepository;
        
    public GetComponentVariants(IContentRepository contentRepository)
    {
        this.ContentRepository = contentRepository;
    }

    public void Process(GetVariantsArgs args)
    {
        // Rendering represents component
        Item componentRendering = this.ContentRepository.GetItem(args.RenderingId);
            
        // Get variants template folder that contains component variants
        Item variants = componentRendering?.Children.FirstOrDefault(item => item.TemplateID.Equals(VariantsTemplateId));
        if (variants == null)
            return;

        args.Variants.AddRange(variants.Children);
    }
}
{% endhighlight %}

This simply loads the rendering item, which is our component, and gets all the variants underneath the 'Variants' template.
{% highlight c# %}
public class GetStyles : GetStylesProcessorBase
{
    private readonly IPresentationContext PresentationContext;
    public readonly IContentRepository ContentRepository;
    private static readonly ID StylesTemplateId = new ID("C6DC7393-15BB-4CD7-B798-AB63E77EBAC4");

    public GetStyles(IPresentationContext presentationContext, IContentRepository contentRepository)
    {
        this.PresentationContext = presentationContext;
        this.ContentRepository = contentRepository;
    }

    public void Process(GetStylesArgs args)
    {
        if (string.IsNullOrEmpty(args.Rendering))
            return;

        ItemUri renderingItemUri = new ItemUri(args.Rendering);
            
        // Rendering represents component
        Item componentRendering = this.ContentRepository.GetItem(renderingItemUri.ItemID);
            
        // Get styles folder for the component
        Item styles = componentRendering?.Children.FirstOrDefault(item => item.TemplateID.Equals(StylesTemplateId));
        if (styles == null)
            return;
            
        args.Styles.AddRange(styles.Children);
    }
}
{% endhighlight %}

Similarly, this loads the rendering item as our component and retrieves the styles as created underneath it in the 'Styles' template item.

### Update your config
Finally, here's the config settings to add the pipelines into the configuration.

{% highlight xml %}
<configuration xmlns:patch="http://www.sitecore.net/xmlconfig/">
  <sitecore>
    <pipelines>
      <getVariants>
        <processor type="YourSolution.Feature.Components.Pipelines.GetVariants.GetComponentVariants, YourSolution.Feature.Components" resolve="true"/>
      </getVariants>
        <getStyles>
            <processor type="YourSolution.Feature.Components.Pipelines.GetStyles.GetStyles, YourSolution.Feature.Components" resolve="true" />           
        </getStyles>
    </pipelines>
  </sitecore>
</configuration>
{% endhighlight %}

### Summary
We've moved the renderings into the site's presentation so that we are better able to organize our components, variants and styles by placing them together. We have also reduced the number of items to create down to 1. The only outstanding items are the javascript and css files. Which live in the media library.

We'll cover how to create these with modal dialogs in the next part of this series.

**Cheers!**
