# MOKICK content proxy for JavaScript

MOKICK runtime templates embedding HTML using an `<iframe>` provide read data access and communication APIs to the iframe content window. This paper describes how to do it from the iframe content window perspective.

## Basics

The communication bridge and it's API is provided by an JavaScript object that will be injected from the parent MOKICK runtime into the `<iframe>` content window. Do not assume it's already available, use a loop to detect it!

We refer to the injected JavaScript object as "MOKICK content proxy".

If the JavaScript object (MOKICK content proxy) is available, read access to data is immediately possible.

Since MOKICK runtimes usually provide complex content handling, navigation and interactions, it's very important to optimize performance within the `<iframe>` content window as well. Therefore MOKICK uses an basic content lifecycle and it's strongly recommended to use the provided handling for initialization, activation, deactivation and finalization!

## Detection

In order to use the MOKICK content proxy we first need to wait until it exists. So use an interval or any kind of asynchronous loop to check if an object named `mokickContentProxySetFromParentWindow` exists in the `<iframe>` window object. We use this very long name to avoid any conflicts with your existing code and recommend you assign the object to you own local variable to enable minification:

```js
// Detect (closure compiler save)
var mokickContentProxy = window["mokickContentProxySetFromParentWindow"];
var mokickContentProxyExists = ( mokickContentProxy != null );

if ( mokickContentProxyExists ) { // Start your logic and stop your detection loop }
```

## Read data

MOKICK content proxy provides read access to text values (strings) and media files using very basic API calls.

Since MOKICK has support for responsive images, different media formats and sizes built in, access to media files is split into two steps. First we need to access available IDs which then can be used to access a list of all available variants of a specific media (again, different file formats or sizes).

## API

### getAudioIds()

Returns an array containing all audio IDs directly linked to this `<iframe>`. If no audios are linked, this function returns `null`.

```js
// EXAMPLE: Log all available audio ids
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
// EXAMPLE: Log all available audio object data
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
// EXAMPLE: Log all available bitmap ids
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
// EXAMPLE: Log all available bitmap object data
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

### getStrings()

Returns an array containing all strings directly linked to this `<iframe>`. If no strings are linked, this function returns `null`.

```js
// EXAMPLE: Log all available strings
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
// EXAMPLE: Log all available video ids
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
// EXAMPLE: Log all available video object data
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
