# iOS Universal Link Support

A main popurse of this article is to introduce my colleague of server side how to coordinate with iOS universal link feature. So I won't talk too much about client side.

## App Schemes And Universal Link
Before iOS 9 if I'd like users jump to my app from other place, I use app scheme that add an "a tag" link on a web page looks like below:
```
<a href="yourAppScheme://action_you_want_app_act">jump to my app</a>
```
The shortage of this solution is if someone didn't install my app, click this "a tag" link wil present a error message alert. That's such a bad user experience.

After iOS 9 we can achieve this by universal link, as [<u>apple offical document</u>](https://developer.apple.com/library/ios/documentation/General/Conceptual/AppSearch/UniversalLinks.html) said, two steps is needed:

- Provide a site which https enabled. and put a file name "apple-app-site-association" to the document root of this site. And the "apple-app-site-association" file should provided by iOS developer, and this file must be sent with the header 'application/pkcs7-mime'.
- The iOS developer register the specific site to the app, so when user click a link " http://the_specified_site/path?query" from other apps , if this url match the rules in the "apple-app-site-association"file. The system may launch my app. otherwise, the system will open safari to load this url. so, you see, no more error message alert, instead a valid web page shows.

Briefly speaking, server side should provide a https site, and put a "apple-app-site-association" file to the document root which written by a iOS developer, last, send the file with the header 'application/pkcs7-mime', [<u>this page</u>](https://gist.github.com/anhar/6d50c023f442fb2437e1) may helpful if you don't konw how to add header 'application/pkcs7-mime'.

## Coordinate Universal Link with Google Search
Occasionally, you may find that click a item of google search results list on iphone, the phone launch an app directly instead of open a web page. how to make this happen?
- Add GoogleAppIndexing sdk to your app, and register your app to google. google document [<u>here</u>](https://developers.google.com/app-indexing/ios/server). 
- For the purpose of making the relationship more clear between web page and app page , the web page being searched should add "App Index" according to [<u>google's regulation</u>](https://developers.google.com/app-indexing/reference/deeplinks#http).  For more detail click [<u>How to Get Your App Content Indexed by Google</u>](https://moz.com/blog/how-to-get-your-app-content-indexed-by-google). 

If any question, feel free to ask.