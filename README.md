# MOKICK content proxy for JavaScript

MOKICK is the elearning authoring tool developed at [https://bayerundpreuss.com](https://bayerundpreuss.com) and used by Bayer und Preuss customers.

HTML content, embedded within a MOKICK runtime, can read data and communicate with the MOKICK web application using a small set of APIs provided and injected by the MOKICK runtime. This paper describes how to use it from within the iframe content window.

## Basics

The communication bridge and it's API is provided by an JavaScript object that will be injected from the parent MOKICK runtime into the `<iframe>` content window. Please do not assume it's already available, use a loop to detect it!

We refer to the injected JavaScript object as "MOKICK content proxy".

If the JavaScript object (MOKICK content proxy) is available, read access to data and address system is immediately possible.

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

Please note: We use this simple detection method just in the first two examples. Feel free to implement your own logic.


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


## Structure and Navigation

MOKICK projects are built upon an hierarchical address system for content organisation. Each address could hold content or exist just for structuring purposes.

**Important: `""` (string with length 0) is the root address.**

MOKICK content proxy provides access to the full project structure and an easy way to navigate to available addresses.

This is the most basic access example:

```js
/* Log all addresses on detection success */
detectMokickContentProxy( function ( mokickContentProxy ) {
  console.log( mokickContentProxy.getAddresses( true ) );
} );
```

For more details and examples please refer to these read data API methods:

- `getAddress()`
- `getAddressChildren()`
- `getAddressContentType()`
- `getAddresses()`
- `getAddressLabel()`
- `getSelectedAddress()`
- `setSelectedAddress()`


## Use content lifecycle

To ensure optimal performance register your own handlers using the method:

- `registerCallbacks()`

See the full example in the API documentation.



## API

### getAddress()

Returns the address of `this` context (iframe). `""` (string with zero length) is the root address. `null` means not available.

```js
/* Log address of this context */
var address = mokickContentProxy.getAddress();
var addressAvailable = ( address !== null );
if ( addressAvailable ) {
  console.log( "Context address is '" + address + "'"  );
}
```

### getAddressChildren( address )

Returns an array containing all direct address children for an `address`. If the given `address` doesn't have children the array is empty. If the given `address` doesn't exist, this function returns `null`.

```js
/* Log address children of address "chapters" */
var address = "chapters";
var addressChilden = mokickContentProxy.getAddressChildren( address );
var addressExists = ( addressChilden !== null );
if ( addressExists ) {
  var childCount = addressChilden.length;
  var hasChildren = ( 0 < childCount );
  if ( hasChildren ) {
    console.log( "Address '" + address + "' has " + childCount + " children: " + addressChilden );
  }
  else {
    console.log( "Address '" + address + "' has no children" );
  }
}
```

### getAddressContentType( address )

Returns the content type for an `address`. If the given `address` doesn't exist or has no content assigned, this function returns `""`.

```js
/* Log the content type of address "chapter-1" */
var address = "chapter-1";
var contentType = mokickContentProxy. getAddressContentType( address );
var addressExists = ( contentType !== null );
if ( addressExists ) {
  console.log( "Address '" + address + "' has content '" + contentType + "'" );
}
```

### getAddresses( includeAddressesWithoutContent )

Returns an array containing all available addresses. Setting `includeAddressesWithoutContent` to `true` will include all, `false` will filter addresses with content only.

```js
/* Log all available addresses */
var addresses = mokickContentProxy.getAddresses( true );
var addressesAvailable = ( addresses !== null );
if ( addressesAvailable ) {
  console.log( addresses.length + " addresses available: " + addresses );
}
```

### getAddressLabel( address )

Returns the label for an `address`. If the given `address` doesn't exist, this function returns `""`.

```js
/* Log the label of address "chapters" */
var address = "chapters";
var label = mokickContentProxy.getAddressLabel( address );
var addressExists = ( label !== null );
if ( addressExists ) {
  console.log( "Address '" + address + "' has label '" + label + "'" );
}
```

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

### getSelectedAddress()

Returns the currently selected address. `""` (string with zero length) is the root address. This address is NOT the address of `this` context (iframe) (see `getAddress()` instead). `null` means not available.

```js
/* Log address of this context and also consider root address */
var address = mokickContentProxy. getSelectedAddress();
var addressAvailable = ( address !== null );
if ( addressAvailable ) {
  console.log( "Selected address is '" + address + "'"  );
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

- `initializeCallback` - Called when initialization should happen. Make sure to call `setInitialized()` when done.
- `activateCallback` - Called when activation should happen. Make sure to call `setActivated()` when done.
- `deactivateCallback` - Called when deactivation should happen. Make sure to call `setDeactivated()` when done.
- `finalizeCallback` - Called when finalization should happen. Make sure to call `setFinalized()` when done.

Please note: While initialization and finalization happen just once, activation and deactivation might happen multiple times (in correct order). For example: Content might fade to the background and will therefore be deactivated, but later fade to the foreground again and then activated again.


```js
/* Load audio metadata on initialize, autoplay on activate and pause on deactivate. */
var setup = function ( mokickContentProxy ) {
  var audioElement = null;
  
  /* Create audio element and set initialized if metadata loaded. If no audio available set initialized immediately. */
  var initialize = function ( mokickContentProxy ) {
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
  var activate = function ( mokickContentProxy ) {
    if ( audioElement != null ) {
      audioElement.play();
    }
    mokickContentProxy.setActivated();
  };

  /* Pause on deactivation and set deactivate. */
  var deactivate = function ( mokickContentProxy ) {
    if ( audioElement != null ) {
      audioElement.pause();
    }
    mokickContentProxy.setDeactivated();
  };
  
  /* Clear audio reference and set finalized. */
  var finalize = function ( mokickContentProxy ) {
    audioElement = null;
    mokickContentProxy.setFinalized();
  };
  
  mokickContentProxy.registerCallbacks( initialize, activate, deactivate, finalize );
}
detectMokickContentProxy( setup );
```

### setSelectedAddress( address )

Sets the currently selected address. `""` (string with zero length) is the root address.

```js
/* Navigate to chapter 1 */
var address = "chapters/chapter-1";
mokickContentProxy.setSelectedAddress( address );
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
