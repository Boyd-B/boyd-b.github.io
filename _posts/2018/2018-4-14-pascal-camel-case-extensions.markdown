---
layout: post
title:  "Convert PascalCase and camelCase to regular case for display | C# Extension"
date:   2018-4-14 11:59
categories: csharp extension
image: https://picsum.photos/400/400/?rnd=414
---

This extension library was created to make the display and manipulation of property names easily convertable from pascal or camel case and into a more display friendly version.

{% highlight c# %}
public static class CasingExtensions
{

	private const string RegExEnglishPattern = "(?<!^)([A-Z][a-z]|(?<=[a-z])[A-Z])";

	/// <summary>
	/// Correctly spaces the enumeration value to make it english-like.
	/// </summary>
	/// <param name="e">The enumeration.</param>
	/// <returns>The english-like form of the enumeration value.</returns>
	public static string ToEnglish(this Enum e)
	{
		return Regex.Replace(e.ToString(), RegExEnglishPattern, " $1");
	}

	/// <summary>
	/// Spaces out the value by Capital letter to make it english readable.
	/// </summary>
	/// <param name="value">'camelCase' or 'PascalCase'</param>
	/// <returns>'camel Case' or 'Pascal Case'</returns>
	public static string ToEnglish(this string value)
	{
		return Regex.Replace(value, RegExEnglishPattern, " $1", RegexOptions.Compiled).Trim();
	}

	/// <summary>
	/// Capitalizes the first character of every word (separated by a space)
	/// </summary>
	/// <param name="value">'some text' or 'SOME TEXT'</param>
	/// <returns>'Some Text'</returns>
	public static string CapitalizeFirstChar(this string value)
	{
		string final = "";
		foreach (var word in value.Split(' '))
		{
			final += word.Substring(0, 1).ToUpper() + word.Substring(1).ToLower() + " ";
		}
		return final.Trim();
	}

	/// <summary>
	/// Converts strings into the following format: camelCase.
	/// </summary>
	/// <param name="str">'some kind of string'</param>
	/// <returns>'someKindOfString'</returns>
	public static string ToCamel(this string str)
	{
		return ToCase(str, true);
	}

	/// <summary>
	/// Converts strings into the following format: PascalCase.
	/// </summary>
	/// <param name="str">'some kind of string'</param>
	/// <returns>'SomeKindOfString'</returns>
	public static string ToPascal(this string str, bool withSpaces = false)
	{
		return ToCase(str, false, withSpaces);
	}

	private static string ToCase(this string str, bool camel, bool withSpaces = false)
	{
		if (string.IsNullOrEmpty(str))
			return str;

		var pieces = str.Split(' ', '.', '/', '-');
		if (!pieces.Any())
			return str;

		StringBuilder sb = new StringBuilder();

		// Then the first word is all lowercase.
		string template = withSpaces ? "{0}{1} " : "{0}{1}";
		if (camel)
			sb.Append(string.Format(template, pieces[0].Substring(0, 1).ToLower(), pieces[0].Substring(1).ToLower()));

		// Remaining words have their first letter upper case, the rest is lower case.
		foreach (string word in pieces.Where(x => !string.IsNullOrWhiteSpace(x)).Skip(camel ? 1 : 0))
			sb.Append(string.Format(template, word.Substring(0, 1).ToUpper(), word.Substring(1).ToLower()));

		return sb.ToString().Trim();
	}

}
{% endhighlight %}

[Example](http://rextester.com/HBYB76114)

**Cheers!**
