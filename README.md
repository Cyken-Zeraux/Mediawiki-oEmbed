# Mediawiki-oEmbed

This is here simply to explain the state of oembed and how it could potentially work with Mediawiki.
This is also here because currently, oembed does not work well with Mediawiki.

Problems:

**1. oEmbed requires a seperate network request for every embed URL.**
  * This can be done to standard using MWHttpRequest.
  * Multiple embeds can cause a edit publish delay because the requests are synchronous
  * It is not possible to standard to use something like multi curl with Mediawiki hooks.
  
**2. oEmbed response must be cached. If it is not, then every edit must make the requests again, a severe performance problem.**
  * Caching can be done using ObjectCache::getMainWANInstance(), and simply checking hashes.
  
**3. If an oEmbed response is malformed, or not retrievable, we cannot get the embed code.**
  * If a previous embed exists, we can send an error message and keep the previous embed code. 
  * If it is a new embed, we must not publish the embed, and give an error message back.
  
**4. oEmbed should support options each service offers for their embeds.**
  * This is **not possible for most services, currently, without a severely hakish config or hard coding every service.**
  

Problem 4 is due to the fact that the oEmbed standard originally does not have any requirement, or suggestion, for a service to manipulate the oEmbed response URL to include options that are available to users on the front-end.

For example, on Kickstarter, a user can choose between a 'full' or 'widget' view of the embed. 

Here is the full view oEmbed response:
```html
"<iframe frameborder="0" height="270.0" scrolling="no" src="https://www.kickstarter.com/projects/rewordable/rewordable-the-uniquely-fragmented-word-game/widget/video.html" width="480"></iframe>"
```
The actual discerner:
/widget/video.html

Here is what you can select in the front end:
```html
<iframe frameborder="0" height="420" scrolling="no" src="https://www.kickstarter.com/projects/rewordable/rewordable-the-uniquely-fragmented-word-game/widget/card.html?v=2" width="220"></iframe>
```
The actual discerner:
widget/card.html?v=2

But, you cannot send oEmbed any information to return this kind of URL. Instead, you must regex the returned URL to create it.

