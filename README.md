# MOKICK content proxy for JavaScript

MOKICK is the elearning authoring tool developed at [https://bayerundpreuss.com](https://bayerundpreuss.com) and used by Bayer und Preuss customers.

HTML content, embedded within a MOKICK runtime, can read data and communicate with the MOKICK web application using a small set of APIs provided and injected by the MOKICK runtime. This paper describes how to use it from within the iframe content window.

## Basics

The communication bridge and it's API is provided by an JavaScript object that will be injected from the parent MOKICK runtime into the `<iframe>` content window. Please do not assume it's already available, use a loop to detect it!

We refer to the injected JavaScript object as "MOKICK content proxy".

If the JavaScript object (MOKICK content proxy) is available, read access to data is immediately possible.

Since MOKICK runtimes usually provide complex content handling, navigation and interactions, it's very important to optimize performance within the `<iframe>` content window as well. Therefore MOKICK uses an basic content lifecycle and it's strongly recommended to use the provided handling for initialization, activation, deactivation and finalization!

## Detection

In order to use the MOKICK content proxy we first need to wait until it exists. So use an interval or any kind of asynchronous loop to check if an object named `mokickContentProxySetFromParentWindow` exists in the `<iframe>` window object. We use this very long name to avoid any conflicts with your existing code and recommend you assign the object to you own local variable to enable minification.

We recommend using this little method. It just expects an success callback which then receives the detected MOKICK content proxy:

```js
/* Detect mokick content proxy and send it to success callback */
var detectMokickContentProxy = function( successCallback ) {
  var detect = function() {
    var mokickContentProxy = window[ "mokickContentProxySetFromParentWindow" ];
    if ( typeof mokickContentProxy == "object" ) {
      if ( typeof successCallback == "function" ) {
        successCallback( mokickContentProxy );
      }
      return;
    }	
    window.requestAnimationFrame( detect );
  }
  detect();
}
```

Please note: We use this simple detection method in all following examples.


## Read data

MOKICK content proxy provides read access to text values (strings) and media files using very basic API calls.

Since MOKICK has support for responsive images, different media formats and sizes built in, access to media files is split into two steps. First we need to access available IDs which then can be used to access a list of all available variants of a specific media (again, different file formats or sizes).

This is the most basic string access example:

```js
/* Log all strings on detection success */
detectMokickContentProxy( function ( mokickContentProxy ) {
  console.log( mokickContentProxy.getStrings() );
} );
```
For more details and examples please refer to these read data API methods:

- `getAudioIds()`
- `getAudioSourceObjects()`
- `getBitmapIds()`
- `getBitmapSourceObjects()`
- `getMediaAlternativeText()`
- `getStrings()`
- `getVideoIds()`
- `getVideoSourceObjects()`

## API

### getAudioIds()

Returns an array containing all audio IDs directly linked to this `<iframe>`. If no audios are linked, this function returns `null`.

```js
/* Log all available audio ids */
var audioIds = mokickContentProxy.getAudioIds();
if ( audioIds != null ) {
  var c = audioIds.length;
  for ( var i = 0; i < c; i++ ) {
    console.log( audioIds[ i ] );
  }
}
```

### getAudioSourceObjects( audioId )

Returns an array of all audio source objects for an `audioId`. If the `audioId` doesn't exist or no urls are available, this function returns `null`.

Each audio source object contains these fields:

- `url` (String - absolute url to audio file)
- `mime` (String - mime type)
- `seconds` (Number - duration in seconds)

```js
/* Log all available audio object data */
var audioId = "Audio ID received by api call";
var audioSourceObjects = mokickContentProxy.getAudioSourceObjects( audioId );
if ( audioSourceObjects != null ) {
  var c = audioSourceObjects.length;
  for ( var i = 0; i < c; i++ ) {
    var sourceObject = audioSourceObjects[ i ];
    console.log( sourceObject["url"] );
    console.log( sourceObject["mime"] );
    console.log( sourceObject["seconds"] );
  }
}
```

### getBitmapIds()

Returns an array containing all bitmap IDs directly linked to this `<iframe>`. If no bitmaps are linked, this function returns `null`.

```js
/* Log all available bitmap ids */
var bitmapIds = mokickContentProxy.getBitmapIds();
if ( bitmapIds != null ) {
  var c = bitmapIds.length;
  for ( var i = 0; i < c; i++ ) {
    console.log( bitmapIds[ i ] );
  }
}
```

### getBitmapSourceObjects( bitmapId )

Returns an array of all bitmap source objects for the `bitmapId`. If the `bitmapId` doesn't exist or no urls are available, this function returns `null`.

Each bitmap source object contains these fields:

- `url` (String - absolute url to audio file)
- `mime` (String - mime type)
- `width` (Number - width in pixel)
- `height` (Number - height in pixel)

```js
/* Log all available bitmap object data */
var bitmapId = "Bitmap ID received by api call";
var bitmapSourceObjects = mokickContentProxy.getBitmapSourceObjects( bitmapId );
if ( bitmapSourceObjects != null ) {
  var c = bitmapSourceObjects.length;
  for ( var i = 0; i < c; i++ ) {
    var sourceObject = bitmapSourceObjects[ i ];
    console.log( sourceObject["url"] );
    console.log( sourceObject["mime"] );
    console.log( sourceObject["width"] );
    console.log( sourceObject["height"] );
  }
}
```

### getMediaAlternativeText( id )

Returns the alternative text for a media (audio, bitmap, video) `id`. If no alternative text is available, this function returns an empty string `""`.

```js
/* Log available alternative text */
var mediaId = "Audio ID received by api call";
var alternativeText = mokickContentProxy.getMediaAlternativeText( mediaId );
if ( 0 < alternativeText.length ) {
  console.log( alternativeText );
}
```

### getStrings()

Returns an array containing all strings directly linked to this `<iframe>`. If no strings are linked, this function returns `null`.

```js
/* Log all available strings */
var strings = mokickContentProxy.getStrings();
if ( strings != null ) {
  var c = strings.length;
  for ( var i = 0; i < c; i++ ) {
    console.log( strings[ i ] );
  }
}
```

### getVideoIds()

Returns an array containing all video IDs directly linked to this `<iframe>`. If no videos are linked, this function returns `null`.

```js
/* Log all available video ids */
var videoIds = mokickContentProxy.getVideoIds();
if ( videoIds != null ) {
  var c = videoIds.length;
  for ( var i = 0; i < c; i++ ) {
    console.log( videoIds[ i ] );
  }
}
```

### getVideoSourceObjects( videoId )

Returns an array of all video source objects for the `videoId`. If the `videoId` doesn't exist or no urls are available, this function returns `null`.

Each video source object contains these fields:

- `url` (String - absolute url to audio file)
- `mime` (String - mime type)
- `width` (Number - width in pixel)
- `height` (Number - height in pixel)
- `seconds` (Number - duration in seconds)

```js
/* Log all available video object data */
var videoId = "Video ID received by api call";
var videoSourceObjects = mokickContentProxy.getVideoSourceObjects( videoId );
if ( videoSourceObjects != null ) {
  var c = videoSourceObjects.length;
  for ( var i = 0; i < c; i++ ) {
    var sourceObject = videoSourceObjects[ i ];
    console.log( sourceObject["url"] );
    console.log( sourceObject["mime"] );
    console.log( sourceObject["width"] );
    console.log( sourceObject["height"] );
    console.log( sourceObject["seconds"] );
  }
}
```

### registerCallbacks( initializeCallback, activateCallback, deactivateCallback, finalizeCallback )

Registers callbacks for content lifecycle handling. Each callback is optional - fallback behaviour exists. If you provide a callback, make sure to call the corresponding completion method whenever ready. See arguments for more details:

- `initializeCallback` - Method will be called when initialization should happen. Make sure to call `setInitialized()` when done.
- `activateCallback` - Method will be called when activation should happen. Make sure to call `setActivated()` when done.
- `deactivateCallback` - Method will be called when deactivation should happen. Make sure to call `setDeactivated()` when done.
- `finalizeCallback` - Method will be called when finalization should happen. Make sure to call `setFinalized()` when done.

Please note: While initialization and finalization happen just once, activation and deactivation might happen multiple times (in correct order). For example: Content might fade to the background and will therefore be deactivated, but later fade to the foreground again and then activated again.


```js
/* Load audio metadata on initialize, autoplay on activate and pause on deactivate. */
var setup = function ( mokickContentProxy ) {
  var audioElement = null;
    /* Create audio element and set initialized if metadata loaded. If no audio available set initialized immediately. */
    var initializeCallback = function ( mokickContentProxy ) {
      var audioIds = mokickContentProxy.getAudioIds();
      if ( audioIds != null && 0 < audioIds.length ) {
        var audioId = audioIds[ 0 ];
        var sourceObjects = mokickContentProxy.getAudioSourceObjects( audioId );
        if ( sourceObjects != null ) {
          audioElement = document.createElement( "audio" );
          audioElement.setAttribute( "controls", "true" );
          audioElement.addEventListener( "loadedmetadata", function( e ) {
            mokickContentProxy.setInitialized();
          });
          var c = sourceObjects.length;
          for ( var i = 0; i < c; i++ ) {
            var sourceObject = sourceObjects[ i ];
            var sourceElement = document.createElement( "source" );
            sourceElement.setAttribute( "type", sourceObject.mime );
            sourceElement.setAttribute( "src", sourceObject.url );
            audioElement.appendChild( sourceElement );
          }
          document.body.appendChild( audioElement );
            return;
          }
          mokickContentProxy.setInitialized();
        }
      };
    /* Autoplay on activation and set activated. */
    var activateCallback = function ( mokickContentProxy ) {
      if ( audioElement != null ) {
        audioElement.play();
      }
      mokickContentProxy.setActivated();
    };
    /* Pause on deactivation and set deactivate. */
    var deactivateCallback = function ( mokickContentProxy ) {
      if ( audioElement != null ) {
        audioElement.pause();
      }
      mokickContentProxy.setDeactivated();
    };
    /* Clear audio reference and set finalized. */
    var finalizeCallback = function ( mokickContentProxy ) {
      audioElement = null;
      mokickContentProxy.setFinalized();
    };
    mokickContentProxy.registerCallbacks( initializeCallback, activateCallback, deactivateCallback, finalizeCallback );
}
detectMokickContentProxy( setup );
```


### setSize( width, height )

Set the size of this `<iframe>`. Implementations differ on platforms and might be supported partially or not at all. Make sure to register proper resize handling.

```js
/* Set custom size and compare result */
var customWidth = 200;
var customHeight = 100;
window.addEventListener( "resize", function( event ) {
  var actualWidth = window.innerWidth;
  var actualHeight = window.innerHeight;
  console.log( "Width custom:" + customWidth + " vs. actual:" + actualWidth );
  console.log( "Height custom:" + customHeight + " vs. actual:" + actualHeight );
});
mokickContentProxy.setSize( customWidth, customHeight );
```


# READ NO FURTHER - REST IS DRAFT  
### mokick

```js

/* This method will find the mokickContentProxy and register all  */
var mokickContentProxyInitializationStart = function ( initializeCallback, activateCallback, deactivateCallback, finalizeCallback, evaluateCallback ) {

		/* Start detection only once! Animate Sometimes calls a frame twice */
	if ( window.mokickContentProxySetFromParentWindowDetectionActive == true ) {return; }
	window.mokickContentProxySetFromParentWindowDetectionActive = true;

	/* Detects the proxy set from parent  */
	var mokickContentProxyDetectionStart = function( onSuccessCallback ) {

		var mokickContentProxyDetection = function() {

			var fromParent = window.mokickContentProxySetFromParentWindow;
			if ( typeof fromParent == "object" ) {

				// Set on this window
				window.mokickContentProxy = fromParent;
				// Call success handler
				onSuccessCallback( fromParent );
				// Done
				return;
			}
			// Try again next frame
			window.requestAnimationFrame( mokickContentProxyDetection );
		}
		// Inital call
		mokickContentProxyDetection();
	}

	/* Success handler */
	var onMokickProxyDetectionSuccess = function( mokickContentProxy ) {

		mokickContentProxy.registerCallbacks( initializeCallback, activateCallback, deactivateCallback, finalizeCallback, evaluateCallback );
	}

	/* Start detection */
	mokickContentProxyDetectionStart( onMokickProxyDetectionSuccess );
};

/* FYI: This variable will hold the received mokick proxy after the detection has succeeded */
window.mokickContentProxy;



/*

Create your callbacks:

*/



/* Mokick requests you to create a static but beautiful view */
var onMokickProxyInitialize = function( mokickContentProxy ) {

	/* This is how you access the text list */
	var strings = mokickContentProxy.getStrings();

	console.log( "onMokickProxyInitialize(", mokickContentProxy, ")" );
	console.log( "strings:", strings );

	/* This is how you enable evaluation for interactions */
	mokickContentProxy.setEvaluable( true );

	/* If the static view is ready - tell mokick about it! */
	mokickContentProxy.setInitialized();
};

/* Mokick requests you to activate all the functionalities or animations now */
var onMokickProxyActivate = function( mokickContentProxy ) {

	console.log( "onMokickProxyActivate(", mokickContentProxy, ")" );

	/* If everything is activated - tell mokick about it! */
	mokickContentProxy.setActivated();
};

/* Mokick requests you to deactivate all the functionalities or animations now */
var onMokickProxyDeactivate = function( mokickContentProxy ) {

	console.log( "onMokickProxyDeactivate(", mokickContentProxy, ")" );

	/* If everything is deactivated - tell mokick about it! */
	mokickContentProxy.setDeactivated();
};

/* Mokick requests you to dispose this view if needed */
var onMokickProxyFinalize = function( mokickContentProxy ) {

	console.log( "onMokickProxyFinalize(", mokickContentProxy, ")" );

	/* If done - tell mokick about it! */
	mokickContentProxy.setFinalized();
};

/* Mokick requests you to evaluate this view */
var onMokickProxyEvaluate = function( mokickContentProxy ) {

	console.log( "onMokickProxyEvaluate(", mokickContentProxy, ")" );

	/* If done - tell mokick if successfull or not! */
	var success = true;
	mokickContentProxy.setEvaluated( success );
};


/*

Start initialization with callbacks!

*/
mokickContentProxyInitializationStart( onMokickProxyInitialize, onMokickProxyActivate, onMokickProxyDeactivate, onMokickProxyFinalize, onMokickProxyEvaluate );
```
