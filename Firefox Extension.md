### First time walkthrough creating a firefox extension
As I continue to use extensions this guide will improve. Most likely there are mistakes here, but this is what I currently know from making two extensions. 
Every extension starts with a json file called manifest.json. Here is a good starting point:
```json
{
  "description": "Extension description",
  "manifest_version": 2,
  "name": "Extension Name",
  "version": "1.0",
  "permissions": [],
}
```
The manifest is really improtant in defining what your extension can and cannot do. The manifest has a version. The one I use is 2 but I think there is a newer one 3. I use 2 because the guides and examples I followed were all on 2.
The permissions array is what the extension wants access to. When a user install an extension firefox notifies you what access it needs. It get's that information from that array. One of my extension uses `["storage", "tabs"]`. I start out with no permissions and once I need it I look up in the documentation which one I should put there.

Now for the actual code. Most often you will need either background scripts or content scripts. When a web page is active they run in the page itself. The difference between the two is that for content scripts you can say for which urls/websites to run on. In my case I used content script and limited it to run only on youtube.com

In my first extension I only used content scripts. This was a problem because I wanted in youtube videos to add an additional div element where I can store my notes. But to create it I would have to look the current html structure of a youtube page and figure out where to insert it as a child element. The problem with this is when youtube changes the layout now the extension doesn't work. Luckly I came across sidebars. You can go to View->Sidebar to see current examples. They are an additional page that opens up and any changes that happen to a website has no affect on your layout. 

The problem with sidebars is that they have no idea what's on the current page. But the content scripts and background scripts do. In my cases I needed to get the current video time. To do this I had to send a message to the content script running in the page. The script is listening for the message and upon recieving it sends the current video time. 

To add a content script I added this to the manifest
```json
"content_scripts": [
    {
      "matches": ["*://*.youtube.com/*"],
      "js": ["content_script.js"]
    }
  ]
```

To add a sidebar I added this to the manifest
```json
  "sidebar_action": {
    "default_title": "Notes With Timestamp",
    "default_panel": "sidebar/panel.html"
  },
```

This is the folder structure
![image](https://github.com/helpfulprojects/HelpfulTech/assets/143523315/258bcca8-2380-4d9c-b694-a60b8db3d300)

To debug the code I personally open the url "about:debugging#/runtime/this-firefox". There I press the "Load Temporary Add-on..." button and find the manifest.json file of my extension. If you console log in your code or use local storage the usual inspector "ctrl+shift+i" will not be of use. Except for background and content scripts that run on the page itself. Each extension has its own inspector. In the same page "about:debugging#/runtime/this-firefox" once the extension is loaded find it and press the inspect button. A new window will show up just for the extension. When you make changes in the code it does not hot reload. In the inspector of the extension just press this button
![image](https://github.com/helpfulprojects/HelpfulTech/assets/143523315/6ce85b54-b79f-4416-9a6d-e49b9025b1ab)

Now once you are done you would want to use it as the other extensions. You cannot just use it. It has to be approved. You have to upload it and most of the time wait 2-3 minutes and you can use it as all other extensions. You have two options, to make it available publicly or for it to be private where you distribute it yourself. I usually pick the second option.

This is for now, I will continue to make improvements to the guide.

Additional resources:
https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Your_first_WebExtension
https://extensionworkshop.com/documentation/publish/package-your-extension/
