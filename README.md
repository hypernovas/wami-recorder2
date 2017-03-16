Wami Recorder for Jquery
================================================

About The Authors
-----------------
* Original Code Taken from https://code.google.com/p/wami-recorder/ (Under MIT License)
    - Ian McGraw
* Code Updates, Tweaks for JQuery and Some fixes By Ignacio Santolin
	- Mail: ignacio.santolin[at]gmail.com
	- Github: https://github.com/isantolin
* Additional bug fixes by Chris Varenhorst (varenc) found while developing lingt.com

Requirements
-----------------
- JQuery: 3.1.1 (for running examples)
- SWFObject: 2.2

Optional Requirements
-----------------
- PHP: 7.0 (for running examples)
- Adobe Flex: 4.6 / Apache Flex 4.15.0 (for development)
- FFmpeg: 3.0.2 w/ libmp3lame, libvorbis support (for server transcoding support)

The Problem
-----------
Some browsers still do not support WebRTC's getUserMedia().  Safari and IE11 are the big outliers.

http://caniuse.com/#search=getUserMedia

This project achieves microphone recording for browsers that support Flash. Using the WAMI recorder, you can collect audio on your server without installing any proprietary media server software.  WAMI can only upload
files in the "wav" format, though I've had good experience using AWS's Elastic Transcoding service to convert them to mp3 for long term storage.

The Solution
------------
The WAMI recorder uses a lightweight Flash app to ship audio from client to server via a standard HTTP POST. Apart from the security settings to allow microphone access, the entire interface can be constructed in HTML and JQuery.

The Client
----------
The Flash app exposes most of its important parameters and functionality to the JavaScript.

```
Wami.startRecording(myRecordURL);
Wami.stopRecording();

Wami.startPlaying(anyWavURL);
Wami.stopPlaying();
```

You can use the well-respected SWFObject library to embed the Flash app, and then access it in the same way as our example code. Take a look at our [quirks](https://code.google.com/p/wami-recorder/wiki/Quirks) page to get acquainted with the idiosyncrasies of Flash and JavaScript on different browsers and operating systems.
If you want to modify the Flash content you can download the free Flex SDK, and compile it from the command line. For a full-fledged IDE, your free options are more limited. For academic use, such as collecting audio for a study via Amazon Mechanical Turk, you can register for a free educational Adobe Flash Builder license.

Interacting with the WAMI flash object directly can be daunting, so it's recommended you use `WamiRecorder.js` found [here](example/client/js/WamiRecorder.js). `WamiRecorder.js` is also currently the best source for documentation on Wami.swf's capabilities.

The Server
----------
If you want to collect audio from the browser, there is no getting around the need to host your own server. However, a key feature of this project is that there is no need to configure an entire Flash Media Server just to collect audio from the web. You can choose whatever server-side technology you prefer. You could, for instance, host this simple PHP script on Apache2:

[example/server/php/writeAudio.php](example/server/php/writeAudio.php)

Notice that this code optionally takes a URL query parameter to specify a file name. With the appropriate permissions, the PHP code will write a file with this nam to disk. You can pass a different file name every time you record to distinguish between individual users, sessions, and utterances. You might wish to use random numbers generated in JavaScript and cookies stored in the browser to track users across browser reloads and to name their corresponding files. It should be noted that the example above suffers from security issues, and should probably be modified for actual deployment.

```
Wami.startRecording('http://localhost/test.php?name=USER.SESSION.UTTERANCE.wav');
```

A slight complication occurs if the URL that you use for playing or recording does not point to the same host that serves the SWF file. In that case, you will need to serve a crossdomain.xml at the root of the host from which the audio is served or recorded.



TODO
----------
- Document WamiRecorder.js library more thoroughly
- Copy documentation over from https://code.google.com/archive/p/wami-recorder/wikis
- Provide an example with more complex security dialogue handling
- Refactor WamiRecorder.js to make some things more clear and easier to develop on
- Support direct to s3 upload for WAV files (provide an example if this is currently possible but this might require updating ActionScript code)
