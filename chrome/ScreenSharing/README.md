Screen Sharing Extension for Chrome
===================================

This extension allows you to use the screen sharing support in Chrome with the [OpenTok.js][ot] client library.

[ot]: http://tokbox.com/opentok/libraries/client/js/

## Testing your unpacked extension

Open chrome://extensions and drag the ScreenSharing directory onto the page, or click 'Load unpacked extension...'. For more information see [Chrome's documentation on loading unpacked
extensions][load-unpacked].

[load-unpacked]: https://developer.chrome.com/extensions/getstarted#unpacked

1. Locate the screensharing-test.html file in the root of this repo. Copy the file to a
   web server. (Screen-sharing access requires that the file be installed on a web server.
   You cannot load the file from a file: URL.)

2. Edit the following values in the file:

   * `apiKey` -- Set this to your OpenTok API key. See https://dashboard.tokbox.com
   * `sessionId` -- An OpenTok session ID
   * `token` -- A valid token for the OpenTok session
   * `extensionId` -- The ID of your screen-sharing extension. You can get the ID
     of the extension in the chrome://extensions page. (It looks like
     `ffngmcfincpecmcgfdpacbdbdlfeeokh`.)

3. In Chrome, navigate to the screensharing-test.html URL on your server. Be sure to load
   the page via HTTPS. (Screen-sharing requires HTTPS.)

   Upon connecting to the OpenTok session, the app publishes a stream using the camera
   as the video source. Click the **Share your screen** button to publish a screen-sharing
   stream.

4. Open a new Chrome window or tab and navigate to the HTTPS screensharing-test.html URL.

   Upon connecting to the OpenTok session, the app publishes a stream using the camera
   as the video source. It also subscribes to the two streams published by the other page
   (the camera stream and the screen-sharing stream).

## Packaging and deploying your extension for use at your website

For your production website, you need to package your screen-sharing extension and register it
in the Chrome Web Store.

See the [Chrome documentation on publishing your app][publish] for details on publishing your extension in the Chrome Web Store.

You can use the [Chrome inline installation][inline] to initiate installation of your extension
"inline" from your site.

In your app, you need to register the ID of the extension using the
<code>OT.registerScreenSharingExtension()</code> method (defined in the OpenTok.js library).
(See the next section and the code in the screensharing-test.html file in this repo.)

[publish]: https://developer.chrome.com/webstore/publish
[inline]: https://developer.chrome.com/webstore/inline_installation

## How to use with OpenTok.js?

After installing the extension (either locally, or after publishing through the Chrome Web Store), get the extension ID from `chrome://extensions` (it looks like `ffngmcfincpecmcgfdpacbdbdlfeeokh`).

```html
<script src="//static.opentok.com/v2/js/opentok.min.js"></script>
```

Register you extension:

```javascript

OT.registerScreenSharingExtension('chrome', 'YOUR-EXTENSION-ID', 2);

```

To test if the extension is available you can use the `OT.checkScreenSharingCapability()` method:

```javascript

OT.checkScreenSharingCapability(function(response) {
  if (!response.supported || response.extensionRegistered === false) {
    // This browser does not support screen sharing
  } else if (response.extensionInstalled === false) {
    // prompt to install the response.extensionRequired extension
  } else {
    // Screen sharing is available
  }
});

```

If you are using [Inline Installation][inline] their are additional APIs you can use.

To publish a screen:

```javascript
var el = document.createElement('div');
document.body.appendChild(el);
var pub = OT.initPublisher(el, {
  name: 'Screen',
  mirror: false,
  audioSource: null,
  videoSource: 'screen',
  maxResolution: { width: 1280, height: 720 }, // max resolution to encode screen in
  width: 1280, // width of preview
  height: 720, // height of preview
}, function(error) {
  if (error) {
    // handle the error
  }
  // resize the publisher preview to match the encoded video
  pub.element.style.width = pub.videoWidth() + 'px';
  pub.element.style.height = pub.videoHeight() + 'px';
})
```

## Browser Support

Google Chrome (version 34+) only.

## More information

See the [OpenTok.js screen-sharing documentation][ot-screensharing].

[ot-screensharing]: https://tokbox.com/opentok/tutorials/screen-sharing/js/

## License

Released under the [MIT license](http://opensource.org/licenses/MIT).
