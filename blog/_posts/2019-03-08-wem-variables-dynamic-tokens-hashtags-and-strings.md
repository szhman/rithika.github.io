---
layout: post
title: "WEM Variables, Dynamic Tokens, Hashtags and Strings"
permalink: "/wem-variables-dynamic-tokens-hashtags-and-strings/"
description: "Working through some advanced WEM filtering and Token capability"
categories: [Apps and Desktops, Citrix, WEM, Windows, XenApp]
redirect_from: 
    - /2019/03/08/wem-variables-dynamic-tokens-hashtags-and-strings
    - /2019/03/08/wem-variables-dynamic-tokens-hashtags-and-strings/
image:
  path: /assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/post_default_image.jpg
sitemap: true
hide_last_modified: true
comments: true
---

<!--excerpt-->

-  Table of Contents
{:toc}

## Intro

One of my favorite parts of WEM is the ability to dynamically build out an environment for a user based on any number of possible values, settings, or variables. 

I've noticed a bit of a trend on the discussions forums as more and more people take on the WEM Solution, around a need for more clarity around how some of the more advanced filtering requirements can be met. I will touch on a few capabilities and some examples of how they may help.

## Active Directory Attribute Condition Matching

Active Directory continues, and I would imagine will continue to be the most utilised "Source of Truth" for user detail and information in most environments. Be it address details, departments, company information or simply group membership management, the amount of information that can be stored in and retrieved from an AD User Object is huge. This offers a very powerful way of storing and retrieving information associated with how a user environment should be presented. 

WEM offers a simple filter type called `Active Directory Attribute Match` and of course, its corresponding `No Active Directory Attribute Match` which can be used to query Active Directory on the fly and assign a set of actions based on the result. The example below shows a simple AD query, looking for the value `Insentra` stored in the `company` attribute of the user account:

[![AD Variable Match Filter Condition]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/1.ADAttrib.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/1.ADAttrib.png)

AD Variable Match Filter Condition
{:.figcaption}

I could also apply a filter rule based on a specific AD attribute simply being anything but blank. That's easy too, simply specify the value of your condition as `?` which aptly converts to `Not Null` 

[![AD Variable Match Filter Condition not Null]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/2.ADAttrib.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/2.ADAttrib.png)

AD Variable Match Filter Condition not Null
{:.figcaption}

## Dynamic Tokens and Active Directory Attributes

Dynamic tokens allow us to expand on a variable and utilize it on the fly for specific use cases in environment management. 

In the below scenario I want to poll the `company` attribute in AD using the `[ADAttribute:Attrib]` Dynamic Token and write a folder redirection path based on the query value. Note that you must specify the AD Attribute *exactly* as its written in the LDAP schema, your best bet to guarantee you get it right is open ADUC, turn on advanced features, and utilize the Attribute Editor Tab on the user account. The Syntax for using this token is as follows: `[ADAttribute:company]`

[![AD company Attribute]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/3.ADAttrib.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/3.ADAttrib.png)

The beauty of Dynamic Tokens is that they can be used in a few different areas around WEM, which allows for extension of capabilities in areas you might not think about, in this example: folder redirection. 

In the Microsoft Policy space, folder redirection is handled typically via a Client Side extension (CSE). With WEM, you can utilize the `[ADAttribute:Attrib]` in your folder redirection path and segment your folder redirection division by any AD attribute you can think of, either by setting a variable and calling the variable in your path, or simply extracting and expanding on the fly as below: 

> `\\Server\Share\[ADAttribute:company]\%UserName%`

The above path in our example scenario would expand for the user James:

> `\\Server\Share\Insentra\J.Nitro`

I have written previously about folder redirection and WEM capabilities [here](https://jkindon.com/2018/06/19/multi-site-and-onedrive-folder-redirection-with-wem/)

## Active Directory OU Level Dynamic Tokens

How about writing some filters or environment variables based on the OU the user belongs to, or even better, the hierarchical placement of that OU? 

The native inbuilt condition here would be `Active Directory Path Match` or `No Active Directory Path Match` which requires you to specify a simple OU path for the condition to be met (don't forget the `*` at the end!). Users in scope of the assignment that live in that specific OU match the condition and thus apply the assigned actions.

[![AD Path Match Filter Condition]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/4.ADAttrib.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/4.ADAttrib.png)

The `[UserParentOU:Level]` expands on this logic and allows you to query on the fly the users home OU, as well as OU's higher up the tree in a numbered approach and write a path, or a variable based on the result. 

Say I have an OU structure as follows:

[![OU Structure]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/5.OU.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/5.OU.png)

I want to write an environmental variable based on the OU that my users are a part of, so that I can then use a filter rule based on that variable to apply mapped drives conditionally 

In this case, I would write an environment variable action called `UserOU` that would use the `[UserParentOU:0]` dynamic token: 

[![dynamic token]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/6.VariableOU.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/6.VariableOU.png)

Once assigned to a group or user in WEM and the user falls within the assignment scope, a variable would be created for the user `James Nitro` with a value of `Insentra`. OU level zero simply means the current OU that the user account resides in. 

A quick breakdown of the logic for the example above using the Level 0 logic:

[![UserParentOU:0]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/Table1.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/Table1.png)

UserParentOU:0
{:.figcaption}

Below shows the variable in action for the user James Nitro:

[![James Nitro UserOU variable]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/7.VariableOU.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/7.VariableOU.png)

My Filter Condition would now be written to say, `Environment variable:` `UserOU` `match` or `not match` my required value:

[![Environmental Variable Match Filter Condition]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/8.VariableMatch.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/8.VariableMatch.png)

Environmental Variable Match Filter Condition
{:.figcaption}

In the same strain, what If I wanted to look at an OU a level or two above due to a common OU being my point of configuration? We would use the same technique here, and add a new environmental variable called say, `UserOUL1` and `UserOUL2`, these values would use the same dynamic token structure with levels 1 and 2 respective, traversing up the OU structure level by level.

[![UserParentOU:1]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/9.VariableUserOU1.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/9.VariableUserOU1.png)

`UserOUL1`  =  `[UserParentOU:1]`
{:.figcaption}

[![UserParentOU:2]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/10.VariableUserOU2.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/10.VariableUserOU2.png)

`UserOUL2`  =  `[UserParentOU:2]`
{:.figcaption}

When these variables are assigned to `James Nitro` the resulting values are shown:

[![Resulting Variables]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/11.VariableOutput.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/11.VariableOutput.png)

Resulting Variables
{:.figcaption}

The end result here for our user base above based on their current AD OU locations would be as follows:

[![UserParentOU:Level Breakdown]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/Table2.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/Table2.png)

UserParentOU:Level Breakdown
{:.figcaption}

My Filter rules can now be extended to use `Environment variable:` `UserOUL1` or `UserOUL2` `match` or `not match` my required value. Nice and powerful.

## Dynamic Token Hashtags and String Operations

There are some really funky capabilities that allow you to delve into all sorts of dark places when it comes to patterns and string operations in WEM, basically the ability to manipulate on the fly a specific value, and call out a component or section of the value, and write it as a variable (I do like variables).

Let's use the simple use case of I want to apply a set of actions based on 3rd octet of my user's IP address of where their connection is sourced from (Client IP Address). The process mapping of this would be as follows:

Client establishes a session and WEM writes a variable called `%CLIENTIPOCT3%` based on the `##ClientIPAddress##` hashtag which is natively available in WEM (note this doesn't work with HTML 5 receiver). This value is then manipulated via a `split` command and the value pulled out and written. In this case I am splitting by a `"."` And specifying the value after the second iteration of the `"."`

Exact Syntax:

> `$Split(##ClientIPAddress##,[\\.],2)$`

[![Variable based on Split of Hashtag]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/12.CLIENTIPOCT3.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/12.CLIENTIPOCT3.png)

I have for demo purposes included a variable of `%ClientIP%` which is based on the `##ClientIPAddress##` hashtag. Now we can see the `%ClientIP%` = `192.168.1.100`, and the 3<sup>rd</sup> octate with the value of `1` has been pulled at set as a new variable called `%CLIENTIPOCT3%`

[![Variables at play]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/13.CLIENTIPOCT3.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/13.CLIENTIPOCT3.png)

WEM then applies a filter rule based on the `Environment variable match` or `not match` of the `%CLIENTIPOCT3%` value.

How about a use case where I wanted to set a variable for the user's full name but needed to remove all spaces (seriously who would need that in reality but it's an easy example). I could use the `RemoveSpaces` String operation and an existing hashtag value from WEM `##FullUserName##` to achieve this.

Exact Syntax:
> `&RemoveSpaces(##FullUserName##)&`

Here is a simple variable set using the `##FullUserName##` Hashtag

[![Hashtag Driven Variable]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/14.SPLIT.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/14.SPLIT.png)

Hashtag Driven Variable
{:.figcaption}

[![Current AD Account]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/15.ADAccount.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/15.ADAccount.png)

Current AD Account
{:.figcaption}

And here is what happened when we applied the String manipulation

[![WEM Config - RemoveSpaces String Manipulation]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/16.FullUserName.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/16.FullUserName.png)

WEM Config - RemoveSpaces String Manipulation
{:.figcaption}

[![Variable Result (no spaces!)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/17.FullUserName.png)]({{site.baseurl}}/assets/img/wem-variables-dynamic-tokens-hashtags-and-strings/17.FullUserName.png)

Variable Result (no spaces!)
{:.figcaption}

Extensibility is huge, it just takes a while to figure out the syntax for some of these capabilities.

## Dynamic Token Hashtags Vs Variables

Hashtags are a replacement for environmental variables, when you want to write an expanded value rather than a literal one in certain instances. When WEM sees a hashtag value, it expands it and uses the expanded value rather than the literal one. This can be particularly handy when writing values within an ini file for example. The example used on the documentation site references the difference between writing an ini file with the value of `%username%` which WEM would literally write as `%username%`, vs using the hashtag of `##username##` which WEM would then expand and write the actual value of `%username%`.

The operating system can process an environmental variable, only WEM can deal with the hashtags Mix and matching WEM hashtags and traditional environmental variables is perfectly acceptable. It's important to understand how WEM treats these as sometimes the behavior might not look as you would expect. You can also quite happily mix dynamic token values such as `ADAttribute:Attrib` and `UserParentOU:Level` on the fly in your paths. This is one of the techniques used to extend the capability of folder redirection as mentioned previously, or create a network file structure structure that matches your OU layout and map a drive accordingly

> `\\Server\Share\[ADAttribute:company]\%UserName%`

Interestingly, when writing this article, I stumbled on some updated content in the WEM [documentation](https://docs.citrix.com/en-us/workspace-environment-management/current-release/reference/dynamic-tokens.html) which looked quite familiar…

That was a direct copy and paste from my discussions answer [here](https://discussions.citrix.com/topic/401286-build-network-drive-path-with-users-ou/) – good to see that Citrix monitors these posts…

## Environmental Variables Processing Order

I focus heavily on the user of environmental variables in many of my articles, the reason for this is simple – it's the most flexible and powerful way of manipulating an environment on the fly based on almost anything you can think of.

The beauty of the WEM processing engine here is that variables are the first thing to be processed by the user agent when it launches. Everything else follows. This makes it extremely flexible and a logical choice for many of the weird challenges you can come across when building out a dynamic user environment

## Conclusion

Not the prettiest of topics to write about, but one that shows some of the basic extensibility of the WEM solution and hopefully helps in demonstrating how with some thinking outside of the square, the use of hashtags, dynamic tokens and environment variables, many different combinations or dynamic controls are available to you and your user base.
