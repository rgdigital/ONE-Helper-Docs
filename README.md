# ONE Helper JS Library

A helper library for creating ONE Creative ads with BeOn video players.

## Getting Started
### Prerequisites

* A [ONE Creative account](http://onecreative.aol.com/)

And if you're building BeOn video overlay ads...

* A [BeOn Portal account](https://beon.vidible.tv/)

### Setting up a BeOn Video overlay ad in ONE Creative
* Create a project folder on your computer to work from.
* Download the [ONE Helper library](https://git.ouroath.com/ryot-uk/ONE-Helper/archive/master.zip), and copy the `build/customAd.js`, `build/one.js`, and `index.html` files to your project folder.
* Save a default backup image into your project folder. [Here is a temporary one you can use](http://placehold.it/640x360).
* In [ONE Creative](http://onecreative.aol.com/), create a new ad by clicking the **New Creative** button.
* Specify **Expand** for format, and save the ad *(Placement Size is irrelevant as it is calculated from the BeOn Wrapper)*.
* Upload the contents of your project folder *(it should contain a html file, customAd.js, one.js, and a backup image)*, saving the backup image via *additional files*.
* In the **Content** panel, set **BeOn Video Ad Settings** > **BeOn Ad** to `True`.
* The ONE preview window will now display a BeOn video player, but it will be showing the default ad set by the helper library.
*  NOTE: If you would like to change the default video used in the preview, you will need the PID and BCID from BeOn to customise. You can get these from the src of the BeOn player you would like to display -
    *  To demonstrate, here is a src from a BeOn player tag - //delivery.vidible.tv/jsonp/pid=58ecef7a8c08e064819dbe2c/554203dbe4b064656db31e16.js
    *  We can see the PID is **58ecef7a8c08e064819dbe2c**
    *  The BCID is the other number, so here it is **554203dbe4b064656db31e16**

So the vidible preview is working, now we need to add your overlay to the preview...

### Displaying your overlay in the demo video player in ONE Creative
* In your ad, create a flight tag by going to **Actions** > **Create Flight** in the sidebar. (Note: Set the **End Date** far in the future and **Target Impressions** really high, like 999999999, as this stuff is handled by the BeOn campaign manager)
* In the sidebar click **Flights**, set **Secure** to `Yes`, then click **Get All Tags**.
* Copy the url from the `src="http://adserve...`part of the tag and replace `' + new Date().getTime() + '` with `[CACHE_BUSTER]`.
* In the **Content** panel, paste the url into CustomAd URL, and click **Save**.

### Adding ONE Helper extension to BeOn
To make sure events are dispatched from videos in BeOn, you must add the helper extension to your player in BeOn. Normally this step will be handled by a campaign manager.
* To add the extension this, go to your player instance, click **Edit**, then add **Extension Loader**.
* Add these settings into the extension, and click save:
    * Extension Config : `{}` *If you want to hide the ad here refer to the showAd method below.*
    * Name : ONE Helper
    * URL : https://cdn.vidible.tv/beon/ext/rmlib/onehelper_beon_extra.js

### Working locally
You can use [Switcheroo](https://chrome.google.com/webstore/detail/switcheroo-redirector/cnmciclhnghalnpfhhleggldniplelbg?hl=en) and a [local server](https://bitnami.com/stack/mamp) to see your file changes locally, without having to repeatedly push to the server. *Note: you will have to Re-push the ad to IQ every time you ad assets, like images, script, or CSS files.*
* Create a script file to write your ads code in, and save it in your project folder.
* In your ads html file, add a reference to the script, like `<script type="text/javascript" src="yourscript.js"></script>`.
* Upload this to your ad in ONE and re-push to IQ so your new script file apepars on the server.
* Install a MAMP server, a good one is [BitNami MAMP](https://bitnami.com/stack/mamp). You can just install without any selecting any of the additional packages like MySQL, Zend, etc. as you will only be using the Apache part of it.
* Once installed, open the **manager-osx** app, and go to the **Manage Servers** tab. If **Apache Web Server** is stopped, select it and press **Start**.
* Copy your project folder to the servers **Document Root** directory.
    * You can find this out in the **manager-osx** app by going to the **Manage Servers** tab, selecting **Apache Web Server**, then clicking **Configure** button, then clicking **Open Config file**.
    * In the config file, search for `DocumentRoot`. This is the path to your local servers root directory, and might look something like `DocumentRoot "/Users/yourusername/bitnami-mampstack/htdocs/"`.
    * Paste your project folder into the htdocs folder.
* In your browser, navigate to your locally hosted script file, something like `https://localhost:8443/yourprojectfolder/yourscript.js`, replacing your own project folder name and script filename.
* If you get a security message click **Advanced**, then **Proceed Anyway**. You should now see the contents of your script file.
* Find the URL for your script file by going to the ONE preview page, and opening the browser dev tools.
* In the Network tab, type your script filename in the filter box. Right-click your script and click 'Copy link address'.
* Now you paste this URL in the **From** box in Switcheroo, then paste the path to your locally hosted script file in the **To** box, then click **Save**.
* You can check to make sure this is working by refreshing the preview in ONE and checking the file path in the Network tab.
* You can now edit your script locally and refresh the ONE preview to see your changes.

## Using the library

Use the $1 ready event to prepare your ad, and instantiate the library with `$1()`

```javascript
$1(window).ready(function() {
  var one = $1();
});
```

<!-- ## Core Methods

### `preloadImage` method

This method is used to preload images assets. Pass it either a string containing the image URL, or an array of image URLs.

```javascript
// Preload single image
// var image = 'im_01.jpg';

// Preload multiple images
var images = [
  'im_01.jpg',
  'im_02.jpg'
]
one.preloadImage(images);
``` -->
## Core Events

### Resize event

Fires on browser window resize. Useful when working with the layout method, as the player wrapper size could change.

```javascript
  one
    .addEventListener('RESIZE', function() {

    });
```


## BeOn Platform Methods

### `layout` method

This method is used to resize the ADTECH container. The paramater is an object containing **height, width, paddingTop, paddingBottom**.
This method takes percentage values to add padding to the player wrapper. This can be used to create space for content. *Note: Currently only supports verticle padding.*

```javascript
one.Platform.BeOn.layout({
  height : '100%',
  paddingTop : '12%',
});
```

### `play` method
Play the ad video.
```javascript
one.Platform.BeOn.play();
```

### `pause` method
Pause the ad video.
```javascript
one.Platform.BeOn.pause();
```

### `volume` method
Changes the volume (accepts parameters of 0-100).
```javascript
one.Platform.BeOn.volume(50);
```

### `mute` method
This mutes the player.
```javascript
one.Platform.BeOn.mute();
```

### `unmute` method
This unmutes the player.
```javascript
one.Platform.BeOn.unmute();
```

### `hideInfoBar` method
This method is used to hide the video info bar (timer bar which appears across the top of BeOn video ads).
```javascript
one.Platform.BeOn.hideInfoBar();
```

### `hideControlBar` method
This method is used to hide the video control bar (scrubber, timer, play, etc.). *NOTE: It is better to avoid removing the controls as this can interfere with BeOn video stats if used incorrectly.*
```javascript
one.Platform.BeOn.hideControlBar();
```

### `showControlBar` method
This shows the video control bar. 
```javascript
one.Platform.BeOn.showControlBar();
```

### `hidePoster` method
This method is used to hide the video poster image. This can be useful as the video poster sits in front of the overlay layer.
```javascript
one.Platform.BeOn.hidePoster();
```

### `showPoster` method
Show the video poster image.
```javascript
one.Platform.BeOn.showPoster();
```

### `showAd` method
Sets the main vidible wrapper to opacity = 1. There is no hideAd method as this is set in the extras page as a config for the `onehelper_beon_extra.js` extra. To do this, add `{"hideAd" : true}` as the config object.
```javascript
one.Platform.BeOn.showAd();
```


### `hidePlayButton` method
Sets the play button to opacity = 0.
```javascript
one.Platform.BeOn.hidePlayButton();
```


### `showPlayButton` method
Sets the play button to opacity = 1.
```javascript
one.Platform.BeOn.showPlayButton();
```

## BeOn Platform Events

You can listen for video events like this:

```javascript
one.Platform.BeOn
  .addEventListener('END', function(e) {
    // fires on video end
  })
```

The following video events are available:

- `PLAY`
- `PAUSE`
- `STOP`
- `SEEK`
- `START`
- `END`
- `TIMEUPDATE` - This returns a time object containing various calculated values (player progress time, percentage complete, percentage remaining, etc.)

The following interaction events are available:

- `SCROLL` - This event fires as the user scrolls the placement page. Returns the scroll position value.
- `INVIEW` - This fires when the ad is at least 50% visible (as in when the user scrolls the placement page so the ad is 50% visible).
- `OUTVIEW` - This fires when the ad is under 50% visible
- `MOUSEENTER` - This fires when the users' mouse cursor hovers over the ad.
- `MOUSELEAVE` - This fires when the users' mouse cursor leaves the ad.