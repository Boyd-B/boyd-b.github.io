---
layout: post
title:  "Sherlock and Anagrams | C# Solution"
date:   2018-4-02 11:59
categories: csharp extension
image: https://picsum.photos/400/400/?rnd=402
---

Two strings are anagrams of each other if the letters of one string can be rearranged to form the other string. Given a string, find the number of pairs of substrings of the string which are anagrams of each other.

It sounds easy enough but like any good problem, There is more depth than can be described easily.

The first example is 'abba' in which there are 4 answers. 'a' at index 0, matches 'a' and index 3. 'ab' matches 'ba', 'b' at 1 matches 'b' at 2. 'abb' matches with 'bba'. However this example lead me down a frightful path of pain and misery. The solution didn;t become clear until I solved it for 'kkkk'. One you would think is the easier of options.

Turns out, this example has everything you need to solve every other problem. They key here was that 'k' could match with multiple positions. So that means, 'k' at index 0 matches with 'k' at index 3. However, 'k' at index 1 also matches with 'k' at index 3. If you can solve for this, the rest is easy.

I created permutations, and multiple hashmaps, it was a mess. Like any good problem the solution was maddingly simple, but you wouldn't have been able to create it without going through the pains of learning all the ways not to write it.

{% highlight c# %}
public class Sherlock{
            string s;
            Dictionary<string, HashSet<int>> map = new Dictionary<string, HashSet<int>>();
            
            public Sherlock(string s){
                this.s = s;
            }
            
            public int Find(){
                for(int size = 1; size < s.Length; size++){
                    for(int start = 0; start <= s.Length-size; start++){
                        string sub = s.Substring(start, size);
                        AddToMap(sub, start);
                    }
                }

                return CountMap();
            }
            private void AddToMap(string sub, int start){
                char[] tmp = sub.ToCharArray();
                Array.Sort(tmp);
                sub = new string(tmp);
                if(!map.ContainsKey(sub))
                {
                    map.Add(sub, new HashSet<int>());
                    map[sub].Add(start);
                    return;
                }

                if(!map[sub].Contains(start))
                    map[sub].Add(start);
            }
            private int CountMap(){
                int total = 0;
                foreach(var list in map.Values){
                    for(int i = list.Count -1; i > 0; i--){
                        total += i;
                    }
                }
                
                return total;
            }
        }
{% endhighlight %}

I'm certain the algorithm can be improved on to have better performance. However, I'm a fan of simplicity and I think this solution is the most easily digestable of any I saw. That's why I wanted to post about it. Sometimes, it's like 'the best camera is the one you have' where the best code solution is one you can understand.

[Example](http://rextester.com/GBOQY95826)

**Cheers!**
