---
layout: post
title: "Getting child elements using Windows Automation in reliable way"
description: ""
category: 
tags: [C#, windows, windows-automation, ui-automation]
---
{% include JB/setup %}

During work on [STAMP](http://stampapp.io) for Windows I needed a reliable way to use other application UI in automated way. Basically I needed to find UI elements like buttons, grids and perform some actions on them. 

On Mac this is easy peasy - you have `Apple Script` which was crafted exactly to do stuff like that. But on Windows there is no one easy way to accomplish this goal. You can use very low level api like `P/Invoke` or try `Windows Automation` or use third party tools to do that. In the end I used mix of `P/Invoke` magic (mostly to send communicates to other application like sending key strokes or clicks) with layer of `Windows Automation` to provide reliable solution. In this blog post I wanted to talk about problem with `Windows Automation` that I encountered.

`Windows Automation API` consists of `FindAll` method to get child/descendants of a given element. For example:

	rootElement.FindAll(TreeScope.Children, PropertyCondition.TrueCondition);

will return collection of all children elements of given `rootElement`... or at least it should. The problem that I experienced was that on some platforms it simply didn't work - the results weren't complete or whole returning collection was empty. The funny thing was that `inspector.exe` - tool provided by `Windows SDK` was doing okay and it also uses `Windows Automation`. 

I fought with that problems for few days and finally I decided to use `RawTreeWalker` and it magically works. Putting it as extension method of `AutomationElement` results in this simple solution:

		public static List<AutomationElement> GetAllChildren(this AutomationElement element)
        {
            var allChildren = new List<AutomationElement>();
            AutomationElement sibling = TreeWalker.RawViewWalker.GetFirstChild(element);

            while (sibling != null)
            {
                allChildren.Add(sibling);
                sibling = TreeWalker.RawViewWalker.GetNextSibling(sibling);
            }

            return allChildren;
        }

and use:

		rootElement.GetAllChildren();

Similarly you can craft recursive method to get all descendants:

        public static List<AutomationElement> GetAllDescendants(this AutomationElement element, int depth = 0, int maxDepth = 3)
        {
             var allChildren = new List<AutomationElement>();

            if (depth > maxDepth){
                return allChildren;
            }

            AutomationElement sibling = TreeWalker.RawViewWalker.GetFirstChild(element);

            while (sibling != null)
            {
                allChildren.Add(sibling);
                allChildren.AddRange(sibling.GetAllDescendants(depth+1, maxDepth));
                sibling = TreeWalker.RawViewWalker.GetNextSibling(sibling);
            }

            return allChildren;
        }
                
        
After getting these elements you can filter, map or do whatever you want with them using LINQ (instead of built in `Windows Automation` conditions):

	element.GetAllChildren().Where(e => e.GetAllChildren().Count > 5); //select child elements of rootElement that had at least 5 children
