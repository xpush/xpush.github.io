---
layout: post
title:  "STALK.IO chrome extension"
date:   2014-10-06 14:08:11
categories: Programming
tags: xpush
og:
  type: article
  description: Brief introduction of chrome extension and explanation how to implement [stalk.io chrome extension](https://chrome.googlecom/webstore/detail/stalkio/kjnhjokbnogkikofagidldnohdbjofoe?hl=ko&utm_source=chrome-ntp-launcher).  
---

Hello. **XPUSH** We are xpush development team.

Stalk.io is a chatting widget service based on XPUSH that you can easily install on your web page.

Thus, apart from the operator or administrator to install manually, You can also use [stalk.io chrome extension](https://chrome.google.com/webstore/detail/stalkio/kjnhjokbnogkikofagidldnohdbjofoe?hl=ko&utm_source=chrome-ntp-launcher) which can talk to each other viewing the same page in your web page.

##About Chrome Extensions?##

Chrome extension is a HTML + JavaScript-based Web applications can be added to the Chrome browser (extension)

It is available for installation in the [Chrome Web Store](https://chrome.google.com/webstore/category/extensions?hl=ko), you can use your web environment faster and more powerful.

The types of chrome app that can be installed in the [Chrome Web Store](https://chrome.google.com/webstore/category/extensions?hl=ko),
[Chrome Extensions](https://developer.chrome.com/extensions)and [Packaged App](https://developer.chrome.com/apps/about_apps), [Hosted App](https://developers.google.com/chrome/apps/docs/developers_guide)

If you are contemplating to choose what type of App, you can find a clue in [Choosing an App Type](https://developer.chrome.com/webstore/choosing).


<img src="https://developer.chrome.com/webstore/images/flowchart.png" width="100%" align="center">


##Extension UI##


Extension UI is run through an icon in the toolbar of the right of the address bar.
Typical Components of Extension UI : popup, [browser action](https://developer.chrome.com/extensions/browserAction), [page action](https://developer.chrome.com/extensions/pageAction).


##Extension Components##

#### 1.[background page](https://developer.chrome.com/extensions/background_pages)

It is composed in HTML and JavaScript files, and is responsible for the main logic of the extension. It will interact with other pages such as Pop-up or contents script in extension.

You can also define a listener of events that occur in the browser action or page action
Is not exposed to the browser, it will be run from the background) as a separate process.
    

#### 2.[Popup page](https://developer.chrome.com/extensions/getstarted)

For information on the page that appears when you click on the extension icon,
using the flickr API [Chrome extension Get Started](https://developer.chrome.com/extensions/getstarted) can be easily understood as an example of a page.

It is possible to interact with background pages.
    

#### 3.[Content Script](https://developer.chrome.com/extensions/content_scripts)

As the script that is inserted into the web page, it is also possible to interact with background pages.



##stalk.io chrome extension##

[stalk.io chrome extension](https://chrome.google.com/webstore/detail/stalkio/kjnhjokbnogkikofagidldnohdbjofoe?hl=ko&utm_source=chrome-ntp-launcher)was developed by the browser action and background page.

manifest.json

<pre data-lang="html">
<code class="prettyprint">
  "browser_action": {
    "default_icon": "images/stalk_16.png"
  },
  "background": {
        "scripts": ["background.js"]
  },
</code>
</pre>

background.js

<pre data-lang="html">
<code class="prettyprint">
  	chrome.browserAction.onClicked.addListener(
	    function(tab) { 
	        chrome.tabs.executeScript(null, { // defaults to the current tab
	            file: "main.js", // script to inject into page and run in sandbox
	            allFrames: false // This injects script into iframes in the page and doesn't work before 4.0.266.0.
	        });
	    }
	);
</code>
</pre>

When you click the icon stalk.io, the event named browswerAction is occured, through the chrome.tabs.executeScript
main.js file is executed.

When you click the icon stalk.io, the event named browswerAction is occured, main.js file is executed through the chrome.tabs.executeScript.

The code included in this main.js file contains code that injects the [stalk.js](http://www.stalk.io/stalk.js) and executes STALK.init().

Please check complete source through the [XPUSH github](https://github.com/xpush/chrome.stalk.io).
