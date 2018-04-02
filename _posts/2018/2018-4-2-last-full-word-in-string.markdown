---
layout: post
title:  "Get the last full word from a string | C# Extension"
date:   2018-4-02 11:59
categories: csharp extension
image: https://picsum.photos/400/400/?rnd=402
---

Here's a quick extension method I created. It's purpose is simple, find the last full word in a string.

{% highlight c# %}
public static string UpToLastFullWord(this string str, int maxLength)
{
	if (str.Length < maxLength)
		return str;
	string longName = str.Substring(0, maxLength < str.Length ? maxLength : str.Length);
	int lastWord = longName.LastIndexOfAny(new[] { ' ', ',', '.', ':', ')' });
	if(lastWord > 0)
		return str.Substring(0, lastWord);
	return str;
}
{% endhighlight %}

First we take the substring of the largest amount. Which is either the max length as specified or the full text. Then we just look for the last stop character available. Typically a space, but it could also be a period, comma or parenthesis. Colons and semi-colons and many other punctuation marks can also be considered a stop character, although this does example not contain a complete list. Update as you feel necessary!

**Cheers!**
