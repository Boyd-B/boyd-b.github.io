---
layout: post
title:  "Appending Query Parameters and Fragments in C#"
date:   2018-2-28 04:34:20
categories: c# .net
highlight: true
image: https://picsum.photos/400/400/
---
Let's kick this blog off with a quick code snippet.

{% highlight c# linenos %}

        public static string UpdateQueryStringParameters(this string url, Dictionary<string, string> parameters)
        {
            Uri uri = new Uri(url);
            var ub = new UriBuilder(uri);
            NameValueCollection queryArgs = HttpUtility.ParseQueryString(uri.Query);
            
            foreach (KeyValuePair<string, string> keyValuePair in parameters)
            {
                queryArgs.Remove(keyValuePair.Key);
                queryArgs.Add(keyValuePair.Key, keyValuePair.Value);
            }

            ub.Query = queryArgs.ToString();
            return ub.Uri.AbsoluteUri;
        }
		
{% endhighlight %}

The purpose of this method is to update a query string if it exists and adds it if it does not.

{% highlight c# linenos %}

        private static UriBuilder MoveQueryArgToFragment(this UriBuilder ub, string argName)
        {
            NameValueCollection queryArgs = HttpUtility.ParseQueryString(ub.Uri.Query);
            if (queryArgs[argName] != null)
            {
                string argValue = queryArgs[argName];
                queryArgs.Remove(argName);
                ub.Query = queryArgs.ToString();
                ub.Fragment = argName + "=" + argValue;
            }
            
            return ub;
        }
		
{% endhighlight %}

This method converts a query string paramater to a url fragment (or hash tag). This could be modified to add an anchor to a url.
