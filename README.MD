  <a href="https://travis-ci.org/Sightengine/client-nodejs">
   <img src="https://travis-ci.org/Sightengine/client-nodejs.svg?branch=master">
  </a>

# About

Use the Sightengine Moderation API to instantly moderate images and videos. See http://sightengine.com for more information.
Before starting, please make sure you have created an account on https://sightengine.com
Please keep in mind that this package is for use with server-side Node that uses Sightengine secret keys.

# Installation
Install the package with:
```javascript
npm install sightengine --save
```

# Initialize the client

You will need your API USER and API SECRET to initialize the client. You can find both of them on your Sightengine account.
```javascript
var sightengine = require('sightengine')('{api_user}', '{api_secret}');
```

# Using Promises
Every method returns a chainable promise which can be used instead of a regular callback.

# Moderate an image
The API accepts both standard still images: JPEG, PNG, WEBP etc. and multi-frame GIF images.

Several moderation engines are available for you to choose from (nudity detection, inappropriate content detection etc...). Please refer to the documentation for more.

## Moderate an image through a public URL:

```javascript
// Detect nudity in an image
sightengine.check(['nudity']).set_url('http://img09.deviantart.net/2bd0/i/2009/276/c/9/magic_forrest_wallpaper_by_goergen.jpg').then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});

// Detect nudity, weapons, alcohol, drugs and faces in an image, along with image properties and type
sightengine.check(['nudity', 'type', 'properties','wad','face']).set_url('http://img09.deviantart.net/2bd0/i/2009/276/c/9/magic_forrest_wallpaper_by_goergen.jpg').then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});
```

## Moderate a local image:
```javascript
// Detect nudity in an image
sightengine.check(['nudity']).set_file('/full/path/to/image.jpg').then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});

// Detect nudity, weapons, alcohol, drugs and faces in an image, along with image properties and type
sightengine.check(['nudity', 'type', 'properties','wad','face']).set_file('/full/path/to/image.jpg').then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});
```

## Moderate a binary image:
```javascript
// Detect nudity, weapons, alcohol, drugs and faces in an image, along with image properties and type
sightengine.check(['nudity', 'type', 'properties','wad','face']).set_bytes(binary_image).then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});
```

### Use fs.createReadStream
```javascript
var binaryImage = fs.createReadStream(path.resolve(__dirname, 'assets', 'image.jpg'));

sightengine.check(['nudity', 'type', 'properties','wad','faces', 'celebrities']).set_bytes(binaryImage).then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});
```

### Use a base64 string
```javascript
// var i64 = image64string;

sightengine.check(['nudity', 'type', 'properties','wad','faces', 'celebrities']).set_bytes(i64, 'image.png').then(function(result) {
   // The result of the API
}).catch(function(err) {
  // Error
});
```

### Use a base64 string from a file
```javascript
var i64 = new Buffer(fs.readFileSync(path.resolve(__dirname, 'assets', 'image.jpg'))).toString('base64');

sightengine.check(['nudity', 'type', 'properties','wad','faces', 'celebrities']).set_bytes(i64, 'image.png').then(function(result) {
   // The result of the API
}).catch(function(err) {
  // Error
});
```

# Video and Stream Moderation

You can perform either _synchronous_ or _asynchronous_ Video Moderation.

* Synchronous Moderation is simple and easy: the Moderation result is provided directly in the reponse to your API request. Synchronous Moderation is only available for videos that are less than 1 minute long.
* Asynchronous Moderation is available for any video or stream. Moderation results are provided through a so-called callback mechanism. You define a callback URL and the Moderation Engine will send back moderation events to that URL in realtime.

## Synchronous Video Moderation

Beware: this is only for videos that have a duration below 1 minute.

```javascript
sightengine.check(['nudity', 'wad']).video_sync('https://sightengine.com/assets/stream/examples/funfair.mp4').then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});
```

## Asynchronous Video Moderation

The first step to moderate a video stream is to submit the video stream to the API, along with a callback URL.

```javascript
sightengine.check(['nudity', 'wad']).video('https://sightengine.com/assets/stream/examples/funfair.mp4', 'https://example.com/yourcallback').then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});
```

Once you have submitted the video, the API will start POSTing moderation updates to your callback URL.

Please see our [Documentation](https://sightengine.com/docs) for more details.

# Feedback
In order to report a misclassification, you need to report the image that was misclassified, the model that was run on this image (models are nudity, face, type, wad), and the correct class of the image.

For each model, there are different classes that you may report. Here are the details:

The nudity model has 3 classes:
 * raw: corresponding to raw nudity
 * partial: corresponding to partial nudity
 * safe: corresponding to no nudity

The face model has 3 classes:
 * none
 * single
 * multiple
 
The type model has 2 classes:
* photo
* illustration

The wad model has 3 classes:
* no-weapons
* weapons
* no-alcohol
* alcohol
* no-drugs
* drugs
 
``` javascript
sightengine.feedback(model, class,image)
```
Example of feedback on a local image:
```javascript
sightengine.feedback("nudity","safe", "/full/path/to/image.jpg").then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});

sightengine.feedback("type","illustration", "/full/path/to/image.jpg") // return a promise
sightengine.feedback("nudity","raw", "/full/path/to/image.jpg") // return a promise
```
Example of feedback through a public URL::
```javascript
sightengine.feedback("nudity","safe", "http://img09.deviantart.net/2bd0/i/2009/276/c/9/magic_forrest_wallpaper_by_goergen.jpg").then(function(result) {
  // The result of the API
}).catch(function(err) {
  // Error
});

sightengine.feedback("type","illustration", "http://img09.deviantart.net/2bd0/i/2009/276/c/9/magic_forrest_wallpaper_by_goergen.jpg") // return a promise
sightengine.feedback("nudity","raw", "http://img09.deviantart.net/2bd0/i/2009/276/c/9/magic_forrest_wallpaper_by_goergen.jpg") // return a promise
```
