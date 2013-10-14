```
     ____    ____    ______  __  __   ____         _____  ____
    /\  _`\ /\  _`\ /\  _  \/\ \/\ \ /\  _`\      /\___ \/\  _`\
    \ \ \L\ \ \ \L\_\ \ \L\ \ \ \/'/'\ \,\L\_\    \/__/\ \ \,\L\_\
     \ \ ,__/\ \  _\L\ \  __ \ \ , <  \/_\__ \       _\ \ \/_\__ \
      \ \ \/  \ \ \L\ \ \ \/\ \ \ \\`\  /\ \L\ \  __/\ \_\ \/\ \L\ \
       \ \_\   \ \____/\ \_\ \_\ \_\ \_\\ `\____\/\_\ \____/\ `\____\
        \/_/    \/___/  \/_/\/_/\/_/\/_/ \/_____/\/_/\/___/  \/_____/
```

# PEAKS.JS

## A browser based audio waveform visualisation frontend component from BBC R&D

### By Chris Finch, Thomas Parisot and Chris Needham.

---------------------------------------

Peaks is a modular frontend component designed for the display of and interaction with audio waveform material in the browser.

Peaks was developed by [BBC R&D](http://www.bbc.co.uk/rd) to allow users to make accurate clippings of audio data over a timeline in browser, using a backend API that serves the waveform data.

Peaks utilizes HTML5 canvas technology to display waveform data at different zoom levels and provides some basic convenience methods for interacting with waveforms and creating time-based visual sections for denoting content to be clipped or for reference, eg: distinguishing music from speech or identifying different music tracks.

See the Project overview here: [http://waveform.prototype0.net/](http://waveform.prototype0.net/)

# Installation

Make sure you have [node.js](http://nodejs.org/) with [npm](https://npmjs.org/) installed on your system (I recommend using [node version manager](https://github.com/creationix/nvm)).

* Install [bower](https://github.com/bower/bower): `npm install -g bower`

* Install [grunt-cli](https://github.com/gruntjs/grunt-cli): `npm install -g grunt-cli`

* Install [sass](http://rubygems.org/gems/sass): `gem install sass` (requires Ruby)

* Clone the project and `cd peaks`

* Install the project dependencies: `npm install`

* Install the frontend dependencies: `bower install`

* Run a development server: `grunt server-dev`

* Run a demo server: `grunt server-demo`

* Build the project for production: `grunt build && cd build`

# Using Peaks.js in your own project

* Build the project: `grunt build`

* Include `build/css/peaks.min.css` and `build/css/peaks.min.js` in your page.

* Add a containing element to your page:

      <div id="peaks-container">

      </div>

### Configuration

The available options for configuration of the viewer are as follows:

```javascript
    var options = {
      /** REQUIRED OPTIONS **/
      container: document.getElementById('peaks-container'), // Containing element
      audioElement: document.getElementById('audio'), // HTML5 Audio element for audio track
      dataUri: '../test_data/sample.dat', // URI to waveform data file in binary or JSON

      /** Optional config with defaults **/
      height: 200, // height of the waveform canvases in pixels
      zoomLevels: [512, 1024, 2048, 4096], // Array of zoom levels in samples per pixel (big >> small)
      keyboard: false, // Bind keyboard controls
      nudgeIncrement: 0.01, // Keyboard nudge increment in seconds (left arrow/right arrow)
      inMarkerColor: '#a0a0a0', // Colour for the in marker of segments
      outMarkerColor: '#a0a0a0', // Colour for the out marker of segments
      zoomWaveformColor: 'rgba(0, 225, 128, 1)', // Colour for the zoomed in waveform
      overviewWaveformColor: 'rgba(0,0,0,0.2)', // Colour for the overview waveform
      segmentColor: 'rgba(255, 161, 39, 1)', // Colour for segments on the waveform
      randomizeSegmentColor: true, // Random colour per segment (overrides segmentColor)

      // Array of initial segment objects with startTime and endTime in seconds and a boolean for editable
      segments: [{
        startTime: 120,
        endTime: 140,
        editable: true,
        color: "#ff0000",
        labelText: "My label"
      },
      {
        startTime: 220,
        endTime: 240,
        editable: false,
        color: "#00ff00",
        labelText: "My Second label"
      }]
    }
```

### Start using require.js

```javascript
    require(['path/to/peaks.min'], function (peaks) {
      peaks.init(options);
    });
```

### Start using vanilla JavaScript

```javascript
    // On document ready
    window.peaks.init(options);
```

#### Advanced configuration

The marker and label Kinetic.js objects may be overridden to give the segment markers or label your own custom appearance (see main.js / waveform.mixins.js, [Kinetic Polygon Example](http://www.html5canvastutorials.com/kineticjs/html5-canvas-kineticjs-polygon-tutorial/) and [Kinetic Text Example](http://www.html5canvastutorials.com/kineticjs/html5-canvas-kineticjs-text-tutorial/)) :

```javascript
    {
      segmentInMarker: mixins.defaultInMarker(api.options),
      segmentOutMarker: mixins.defaultOutMarker(api.options),
      segmentLabelDraw: mixins.defaultSegmentLabelDraw(api.options)
    }
```

# API

The top level peaks object exposes the following API for interaction with the widget:

<table>

  <tr>
    <th>Method</th>
    <th>Description</th>
  </tr>

  <tr>
    <td>`peaks.init(options)`</td>
    <td>Start a instance of peaks with the assigned options.</td>
  </tr>

  <tr>
    <td>`peaks.time.getCurrentTime()`</td>
    <td>Returns currently selected time in seconds (convenience method interchangeable with audioElement.currentTime).</td>
  </tr>

  <tr>
    <td>`peaks.zoom.zoomIn()`</td>
    <td>Zoom in the waveform zoom view by one level.</td>
  </tr>

  <tr>
    <td>`peaks.zoom.setZoom(indexInZoomArray)`</td>
    <td>Set the zoom level to the element in the `options.zoomLevels` array at index `indexInZoomArray`.</td>
  </tr>

  <tr>
    <td>`peaks.zoom.getZoom()`</td>
    <td>Return the current zoom level.</td>
  </tr>

  <tr>
    <td>`peaks.segments.addSegment(startTime, endTime, editable, labelText)`</td>
    <td>Add a segment to the waveform timeline with starting time `startTime` (seconds), ending time `endTime` (seconds) and set whether the segment is user editable with `editable` (boolean, defaults to `false`). Alternatively, provide an array of segment objects as shown above in the config options as the first and only argument to add all those segments at once.</td>
  </tr>

  <tr>
    <td>`peaks.segments.getSegments()`</td>
    <td>Returns an array of objects representing all displayed segments present on the timeline in the format:

```javascript
        [{
          color: "rgba(123, 2, 61, 1)", // Assigned colour of the segment
          editable: true, // Editable state of the segment
          endTime: 588.986667, // End time in seconds of the segment
          id: "segment0", // Unique ID of the segment
          overview: Kinetic.Group, // Kinetics.js Element group of segment canvas objects for overview waveform
          startTime: 578.986667, // End time in seconds of the segment
          zoom: Kinetic.Group // Kinetics.js Element group of segment canvas objects for overview waveform
        }]
```

    </td>
  </tr>
</table>

# Development

* Run the development server: `grunt server-dev`

* Edit files in `lib/js/`, `lib/sass` and `lib/templates/handlebars` as required.

* The development page will recompile and refresh on save of any file.

# Testing

`grunt test` should work for simple one time testing. If you are developing and want to repeatedly run tests in a browser on your machine first launch the test server with `grunt karma:unit` and then run the tests with `grunt karma:unit:run` in a separate terminal tab.

License
=======

See [COPYING](COPYING)

This project includes audio from the [Wellcome Trust's Tree of Life video](http://www.wellcometreeoflife.org/), used under the terms of the [Creative Commons Attribution-NonCommercial-ShareAlike 2.0 UK: England & Wales license](http://creativecommons.org/licenses/by-nc-sa/2.0/uk/).

Authors
=======

### British Broadcasting Corporation

- [Chris Finch](http://github.com/chrisfinch)
- [Thomas Parisot](https://github.com/oncletom)
- [Chris Needham](http://github.com/chrisn)


Copyright
=========

Copyright 2013 British Broadcasting Corporation