# Bistri Conference JS

Audio and Video calls made easy using Bistri Conference JS library and Bistri Signaling Server

## Browsers support

| [<img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png" alt="Firefox" width="24px" height="24px" />](http://godban.github.io/browsers-support-badges/)</br>Firefox | [<img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png" alt="Chrome" width="24px" height="24px" />](http://godban.github.io/browsers-support-badges/)</br>Chrome | [<img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png" alt="Safari" width="24px" height="24px" />](http://godban.github.io/browsers-support-badges/)</br>Safari | [<img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png" alt="Firefox" width="24px" height="24px" />](http://godban.github.io/browsers-support-badges/)</br>Edge | [<img src="https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png" alt="Opera" width="24px" height="24px" />](http://godban.github.io/browsers-support-badges/)</br>Opera
| --------- | --------- | --------- | --------- | --------- |
| 52+ | 69+ | 11+ | 79+ | 56+


## Setup

**Include the library**

  ```html
<script type="text/javascript" src="https://cdn.jsdelivr.net/gh/bistri/bistri-conference-js/dist/bistri.conference-3.4.0.js">
  ```

---

## Properties

### version

Actual Bistri Conference JS version

```js
bc.version // 3.4.0
```



## Methods

### init ( options )

Initialize Bistri Conference JS.
Application ID and key could be retreived from your developer account https://api.developers.bistri.com/admin/applications.

> We strongly recommend to set a referrer filter to prevent your application keys from being used by anybody. You can set it when editing your application parameters. Eg: my.domain.com* (don’t forget to add the wildcard suffix to match all your referrers)

```js
bc.init( {
    appId: "e3bvcx908",
    appKey: "edhh64ksMpnaCcX8r29uOjH9v35",
    userName: "John Doe"
} );
```

**options**
- userId: `String`, user id, if empty a random id is provided
- userName : `String`, user display name
- appId: `String`, application ID
- appKey: `String`, application key
- turnServers: `Array`, alternative TURN servers. 
    ```js
    [ { urls: [ 
            "stun:stun.l.google.com:19302",
            "stun1.l.google.com:19302"
    ] } ]
    ```
- stunServers: `Array`, alternative STUN servers. 
    ```js
    [ { 
        urls: [ "turn:turn.anyfirewall.com" ],
        username: "webrtc",
        credential: "webrtc"
    } ]
    ```
- forceTURN: `Boolean`, force connection to be relayed by TURN server (default: `false`)
- autoCloseOnExit: `Boolean`, force disconnection when closing window (default: `true`)
- autoReconnect: `Boolean`, automatically reconnect after disconnection (default: `false`)
- autoAcceptCall: `Boolean`, automatically accept incoming calls (default: true)
- debug: `Boolean`, show debug logs (default: `false`)
- statsInterval: `Number`, number of seconds between 2 measures, set to 0 to disable stats (default: `4`)

---

### set

Update Bistri Conference options after init.

```js
bc.set ( {
    statsInterval: 2,
    debug: true
} );
```

---

### connect ()

Connect the JS client to the signaling server.

```js
bc.connect ();
```

**events**

Event `connected` is triggered when the session has been successfully opened, otherwise event `error` is triggered.

---

### disconnect ()

Disconnect the JS client from the signaling server.

```js
bc.disconnect ();
```

**events**

Event `disconnected` is triggered when the session has been closed.

---

### isConnected ()

Get client connection status.

Return a `Boolean`.

```js
console.log ( bc.isConnected () );

// true
```

---

### isCompatible ()

Check if browser is compatible with Bistri Conference.

Return a `Boolean`.

```js
console.log ( bc.isCompatible () );

// true
```

---

### isScreenSharingCompatible ()

Check if browser is compatible with screen-sharing feature.

Return a `Boolean`.

```js
console.log ( bc.isScreenSharingCompatible () );

// true
```
---

### getClient ()

Get client properties
- browser
- cpu (if available)
- device (if available)
- rendering engine
- os

Return an `Object`.

```js
console.log ( bc.getClient () );

// { 
//    browser: { name: "Chrome", version: "83.0.4103.106", major: "83" }
//    cpu: { architecture: undefined }
//    device: { vendor: undefined, model: undefined, type: undefined }
//    engine: { name: "Blink", version: "83.0.4103.106" }
//    os: {name: "Mac OS", version: "10.13.6"}
// }
```

---

### getMediaSources ( callback )

List all available audio and video devices.

Return an `InputDeviceInfo Array`.

```js
bc.getMediaSources ( devices => { console.log ( devices )) } );

// [ {deviceId: "default", groupId: "81bc13...84f48", kind: "audioinput", label: "Internal Microphone"}, { ... } ]
```

**arguments**
- callback: `Function`, function to call when devices list is available

---

### onMediaSourcesUpdated ( callback ) <sup>`new`</sup>
Trigger callback when the list of available audio/video devices has changed

```js
bc.onMediaSourcesUpdated ( () => {
    bc.getMediaSources ( devices => { console.log ( devices ) ) } 
} ) );

// [ {deviceId: "default", groupId: "81bc13...84f48", kind: "audioinput", label: "Internal Microphone"}, { ... } ]
```

---

### startStream ( constraints, success, error )

Get a new audio/video stream.

```js
bc.startStream ( {
    audio: true, 
    video: {
        width: { min: 1024, ideal: 1280, max: 1920 },
        height: { min: 576, ideal: 720, max: 1080 }
    } 
}, newStream => console.log ( newStream ), error => console.log ( error ) );

bc.startStream ( "screen-sharing:application:12", newStream => console.log ( newStream ) );

// MediaStream { ... }
// MediaStream { ... }
```

> In order to be allowed to request a stream your page *must* be loaded over **HTTPS**

**arguments**
- constraints: 
    - `Object`, request a new audio/video stream, [see details here](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia),
    - `String`, request a new screen-sharing stream, "screen-sharing[:framerate]"
- success: `Function`, function called when the stream is available
- error: `Function`, function called when an error occurred
    - NotFoundError: requested audio/video device not found
    - NotReadableError: requested audio/video not available or is used by another software
    - OverconstrainedError: requested constraints cannot be fulfilled
    - NotAllowedError: user refused to grant access to requested device

---

### updateStream ( stream, constraints, success, error )

Update stream with new constraints.

```js
bc.updateStream ( localStream, { 
    video: { 
        frameRate: { min: 5, ideal: 10, max: 15 }
    }
}, updatedStream => console.log ( updatedStream ), error => console.log ( error ) );

// MediaStream { ... }
```

**arguments**
- stream: `MediaStream`, stream to stop
- constraints: `Object`, [see details here](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)
- success: `Function`, function called when the stream is updated
- error: `Function`, function called when an error occurred
    - NotFoundError: requested audio/video device not found
    - NotReadableError: requested audio/video not available or is used by another software
    - OverconstrainedError: requested constraints cannot be fulfilled
    - NotAllowedError: user refused to grant access to requested device

---

### stopStream ( stream, callback )

Stop specified stream.

```js
bc.stopStream ( stream, stoppedStream => console.log ( stoppedStream ) );

// MediaStream { ... }
```

**arguments**
- stream: `MediaStream`, stream to stop
- callback: `Function`, function to call when the stream is stopped

---

### attachStream ( stream, node [, options] )

Attach stream to the DOM to render it.

```js
bc.attachStream ( stream, document.body, { mirror: true } );
```

**arguments**
- stream: `MediaStream`, stream to render
- node: `DOM node`, could be a video/audio player or a container
- options
    - mirror: `Boolean`, flip video vertically, like in a mirror (default: `false`)
    - controls: `Boolean`, display audio/video controls (default: `false`)
    - fullscreen: `Boolean`, toggle fullscreen when clicking on the video (default: `false`)

---

### reattachStream ( stream, node [, options] ) <sup>`new`</sup>

Refresh stream rendering after it had been updated.

```js
bc.reattachStream ( stream, document.body, { mirror: true } );
```

**arguments**
- stream: `MediaStream`, stream to render
- node: `DOM node`, could be a video/audio player or a container
- options
    - mirror: `Boolean`, flip video vertically, like in a mirror (default: `false`)
    - controls: `Boolean`, display audio/video controls (default: `false`)
    - fullscreen: `Boolean`, toggle fullscreen when clicking on the video (default: `false`)

---

### detachStream ( stream )

Destroy stream rendering.

```js
bc.detachStream ( stream );
```

**arguments**
- stream: `MediaStream`, stream which rendering will be destoyed

---

### cloneStream ( stream ) <sup>`new`</sup>

Clone a stream.

Return a new `MediaStream` object.

```js
var newStream = bc.cloneStream ( localStream );
```

**arguments**
- stream: `MediaStream`, stream to clone

---

### mergeStream ( stream, target [, stop] ) <sup>`new`</sup>

Override all audio/video tracks of the targeted stream with the tracks of the specified stream.

Return the target stream.

```js
bc.mergeStream ( newStream, localStream );
```

**arguments**
- stream: `MediaStream`, stream that will be used as source
- target: `MediaStream`, stream that will be overriden
- stop: `Boolean`, if true, overriden tracks will be stopped (default: false)

---

### dummyStream () <sup>`new`</sup>

Create a stream with no audio/video tracks.

```js
var emptyStream = bc.dummyStream ();
```

---

### setTrackDescription ( stream, kind, description ) <sup>`new`</sup>

Set a description for audio or video track of the specified stream. The description will be available on the remote side.

```js
bc.setTrackDescription ( localStream, "video", "Front Camera" );

console.log ( localStream.videoDesc )

// Front Camera
```

**arguments**
- stream: `MediaStream`, targeted stream
- kind: `String`, type of track: `audio` or `video`
- description: `String`, track description

---

### stopTrack ( stream, kind [, remove] ) <sup>`new`</sup>

Stop audio or video track for a given stream.

```js
bc.stopTrack ( localStream, "video" );

// Front Camera
```

**arguments**
- stream: `MediaStream`, targeted stream
- kind: `String`, type of track: `audio` or `video`
- remove: `Boolean`, if true, track is removed from stream after being stopped (default: `false`)
---

### joinRoom ( name [,capacity] )

Join a conference room. The max number of participants can be limited by setting `capacity` option when first user join the room. If the room doesn’t exist, it will be created.

```js
bc.joinRoom ( "myConferenceRoom", 4 );
```
**arguments**
- name: `String`, room name
- capacity: `Number`, max participants

**events**

Event `joinedRoom` is triggered when user has successfully joined the room, other members already present into the room will receive an event `peerJoinedRoom`. Event `joinRoomError` is triggered otherwise

---

### quitRoom ( name )

Quit conference room.

```js
bc.quitRoom ( "myConferenceRoom" );
```

**arguments**
- name: `String`, room name

**events**

Event `quittedRoom` is triggered when the user has leaved the room, other room members will receive event `peerQuittedRoom`. Otherwise an event `quitRoomError` is triggered.

---

### call ( userid, room, options )

Start a call with a remote user.

```js
bc.call ( "user-1", "myConferenceRoom", { stream: [
    localStream,
    otherStream
] } );
```

> Note: multiple streams can be set at the same time. Eg: person + screen-sharing

**arguments**

- userid : `String`, remote user id
- room: `String`, room name
- option:
    - stream: `MediaStream` or `MediaStream Array`, stream(s) to use for the call
    - sendonly: `Boolean`, when `true` no stream is expected to be sent back (default: `false`)
    - audio-codec: `String`, set prefered audio codec (opus/48000/2, multiops/48000/5, ...)
    - audio-codec: `String`, set prefered video codec (VP8, VP9, H264, ...)
    - audio-bitrate: `Number`, set max audio bitrate sent/recv (kbp/s)
    - video-bitrate: `Number`, set max video bitrate sent/recv (kbp/s)

**events**

- If the caller hasn’t joined the room previously, he will be automatically added to the room and event `joinedRoom` will be triggered.
- If the callee is already present in the room: the call is processed and event `streamAdded` is triggered shortly after on both sides when the remote audio/video stream is received.
- If the callee is not present in the room: event `incomingRequest` is triggered on the remote user side to invite him to join the call.
- If the call fails an event `callFailed` is triggered on both caller and callee sides.

---

### updateCall ( userid, room, options ) <sup>`new`</sup>

Update an existing call.

```js
bc.updateCall ( "user-1", "myConferenceRoom", { stream: [ 
    { update: localStream },
    { add: newStream }
] } );
```

> Note: multiple streams can be modified at the same time

- userid : `String`, remote user id
- room: `String`, room name
- option:
    - stream: `Object` or `Object array`, actions to operate on streams
        - update: `MediaStream`, update the actual media
            >
                { stream: { update: localStream } }
        - add: `MediaStream`, add a new media to the call
            >
                { stream: { add: localStream } }
        - remove: `MediaStream`, remove media from the call
            >
                { stream: { remove: localStream } }
        - copy: `MediaStream Array`, copy tracks from media A to media B and update the call
            >
                { stream: { copy: [ newStream, localStream ] } }
    - audio-bitrate: `Number`, set max audio bitrate sent/recv (kbp/s)
    - video-bitrate: `Number`, set max video bitrate sent/recv (kbp/s)

---

### endCall ( userid, room )

End a call with a user.

```js
bc.endCall ( "user-1", "myConferenceRoom" );
```

**arguments**
- userid : `String`, remote user id
- room: `String`, room name

**events**

Event `streamClosed` is triggered on both sides.

---

### endCalls ( room )

End call with all the users in a room.

```js
bc.endCalls ("myConferenceRoom" );
```

**arguments**
- room: `String`, room name

**events**

Event `streamClosed` is triggered on the both sides for each call.

---

### openDataChannel ( userid, label, room )
Create a new data channel with a remote participant.

```js
bc.openDataChannel ( "user-1", "chat", "myConferenceRoom" );
```

**arguments**
- userid : `String`, remote user id
- label: `String`, data channel label
- room: `String`, room name

**events**

- If the caller hasn’t joined the room previously, he will be automatically added to the room and event `joinedRoom` will be triggered, then the event `dataChannelCreated` is triggered.
- If the callee is already present in the room: the request is processed and the event `dataChannelRequested` is triggered.
- If the callee is not present in the room: event `incomingRequest` is triggered on the remote user side to invite him to accept the request.
- If the request fails an event `callFailed` is triggered on both caller and callee sides.

---

### sendFile ( userid, room, path ) <sup>`new`</sup>

Send a file to remote participant(s).

```js
bc.sendFile ( "user-1", "myConferenceRoom", "file://..." );
```

**arguments**
- userid : `String`, remote user id
- room: `String`, room name
- path: `String`, file path

**events**

- If the sender hasn’t joined the room previously, he will be automatically added to the room and event `joinedRoom` will be triggered
- when a new file transfert is initiated event `fileTransferWaiting` is triggered on sender side, while event `newFileTransfer` is triggered on recipient side
- if file transfert has been declined by recipient, event `fileTransferAborted` is triggered on both sides
- once recipient has accepted file transfert, event `fileTransferProgress` is triggered on both sides periodically to update transfert progression and event `fileTransferBitrate` is triggered periodically to update transfert bitrate
- once file transfert is completed, event `fileSent` is triggered on sender side and event `fileReceived` is triggered on reciever side

---

### sendCustomSignal ( userid, room, data ) <sup>`new`</sup>

Send a customized signal to a conference participant.

```js
bc.sendFile ( "user-1", "myConferenceRoom", { action: "wizz" } );
```

**arguments**
- userid : `String`, remote user id
- room: `String`, room name
- data: `String` or `Object`, request payload

**events**
- event `onCustomSignal` is triggered on the remote participant side

---

### getLocalStreams ()

return a `MediaStream Array` for local streams.

```js
console.log ( bc.getLocalStreams () );

// [ `MediaStream`, `MediaStream`, ... ]
```

---

### getRemoteStreams ( room )

return an `Object` containing pairs of remote userid / `MediaStream`.

```js
console.log ( bc.getRemoteStreams ( "myConferenceRoom" ) );

// { "St9d9JU7": `MediaStream`, ... }
```

**arguments**
- room: `String`, room name

---

### getLocalTracks () <sup>`new`</sup>

return an `Object` containing pairs of remote userid / `Array` of sent `MediaStreamTrack`.

```js
console.log ( bc.getLocalTracks () );

// { "St9d9JU7": [ `MediaStreamTrack`, MediaStreamTrack ] }
```

---

### getRemoteTracks ( room ) <sup>`new`</sup>

return an `Object` containing pairs of remote userid / `Array` of received `MediaStreamTrack`.

```js
console.log ( bc.getRemoteTracks ( "myConferenceRoom" ) );

// { "St9d9JU7": [ `MediaStreamTrack`, MediaStreamTrack ] }
```

**arguments**
- room: `String`, room name

---

### getDataChannels ( room )

Return an `Object` containing pairs of remote userid / `DataChannel`.

```js
console.log ( bc.getDataChannels ( "myConferenceRoom" ) );

// { "St9d9JU7": `DataChannel`, ... }
```

**arguments**
- room: `String`, room name

---

### getFileTransferts ( room )  <sup>`new`</sup>

Return an `Object` containing pairs of remote userid / `FileTransfert`.

```js
console.log ( bc.getFileTransferts ( "myConferenceRoom" ) );

// { "St9d9JU7": `FileTransfert`, ... }
```

**arguments**
- room: `String`, room name

---

### getRoomMembers ()

Return an `Array` containing room members.

```js
console.log ( bc.getRoomMembers ( "myConferenceRoom" ) );

// [ { "id": "St9d9JU7", name:"Jane Smith" }, ... ]
```

**arguments**
- room: `String`, room name

---

### getPresence ( userid )  <sup>`deprecated`</sup>

Request remote user presence status.

```js
bc.getPresence ( 'rfpWEk' );
```
**arguments**
- userid : `String`, remote user id

**events**
- Event `presence` is triggered when the presence status is received.

---

### setPresence ( status )  <sup>`deprecated`</sup>

Set local user presence status.

```js
bc.setPresence ( 'online' );
```
**arguments**
- status : `String`, presence status: online, away, busy, offline (default: online)

---

### allowMonitorAudioActivity ()

Allow Bistri Conference to access to audiocontext. Must be called on a user gesture.

> Necessary to use `monitorAudioActivity` method

```js
document.addEventListener ( "click", bc.allowMonitorAudioActivity );
```

---

### monitorAudioActivity ( stream, callback )

Monitor audio ouput level for a given `MediaStream`.

> Level is expressed in percent (0-100%)

```js
bc.monitorAudioActivity ( localStream, level => console.log ( level ) )

// 56
// 20
// 25
```

**arguments**
- stream: `MediaStream`, stream to monitor
- callback: `Function`, function to call each time there is a level change

---

### muteSound ( stream, mute )

Mute audio ouput for a given stream.

```js
bc.muteSound ( localStream, true );
```

**arguments**
- stream: `MediaStream`, stream to mute
- mute: `Boolean`, mute status

---

### isSoundMuted ( stream )

Get audio ouput status for a given stream.

Return a `Boolean`.

```js
console.log ( bc.isSoundMuted ( localStream ) );

// true
```
**arguments**
- stream: `MediaStream`, checked stream 

---

### muteAllSounds ( mute )

Mute audio ouput for every incoming streams.

```js
bc.muteAllSounds ( true );
```

**arguments**
- mute: `Boolean`, mute status

---

### isAllSoundsMuted ()

Get audio ouput status.

Return a `Boolean`.

```js
console.log ( bc.isAllSoundsMuted ( localStream ) );

// true
```

---

### muteVideo ( stream, mute )

Mute video input for a given stream.

```js
bc.muteVideo ( localStream, true );
```

**arguments**
- stream: `MediaStream`, stream to mute
- mute: `Boolean`, mute status

**events**
- Event `streamMuteChange` is triggered on the remote side.

---

### toggleVideo ( stream )  <sup>`new`</sup>

Instead of muting video track this method add/remove video track from current calls.

Return a `Boolean` reflecting the current video input status.

```js
bc.toggleVideo ( localStream );
```

**arguments**
- stream: `MediaStream`, stream to toggle

**events**
- Event `streamMuteChange` is triggered on the remote side.

---

### isVideoMuted ()

Get video input status.

Return a `Boolean`.

```js
console.log ( bc.isVideoMuted () );

// true
```

---
### muteMicrophone ( mute )

Mute audio input.

```js
bc.muteMicrophone ( true );

// true
```

**arguments**
- mute: `Boolean`, mute status

---

### isMicrophoneMuted ()

Get audio input status.

Return a `Boolean`.

```js
console.log ( bc.isMicrophoneMuted () );

// true
```

---

## Signaling Events

### bc.signaling.bind ( event, handler )

Bind signaling events.

Return a reference associated to the handler.

```js
const handler_ref = bc.signaling.bind ( event, data => {} );
```

### bc.signaling.unbind ( event, handler )

Unbind can be done using handler reference (remove a specific handler) or by an event name (remove all handlers)

```js
bc.signaling.unbind ( handler_ref );

// or

bc.signaling.unbind ( event );
```

### Events

- **error**: triggered when a global error occurred.
    - arguments:
        - error `JSON`
        ```json
        {
            "code": "xxx", // error code
            "text": "..."  // error reason
        }
        ```
- **connected**: triggered when client is connected to the signaling server.
    - event related to: `connect()`
    - arguments:
        - session `JSON`
        ```json
        {
            "id": "rfpWEk" // user session id
        }
        ```
- **disconnected**: triggered when client is gracefully disconnected.
    - event related to: `disconnect()`
- **connectionResumed**: triggered when client reconnect after a disconnection.
- **connectionError**: triggered when connection to signaling server is lost.
    - arguments:
        - error `JSON`
        ```json
        {
            "code": "xxx",  // error code
            "reason": "..." // error reason
        }
        ```
- **joinedRoom**: triggered when room has been joined
    - event related to: `joinRoom()`
    - arguments:
        - data `JSON`
        ```json
        {
	        "room": "aUpS45g7J0w",   // room name
	        "members":[              // array of users already present in the room
                { 
                    id: "aVlxUi",    // remote peer id
                    name: "Jane Doe" // remote user display name
                },
	        ],
        }
        ```
- **joinRoomError**: triggered when an error occurred while joining room
    - event related to: `joinRoom()`
    - arguments:
        - error `JSON`
        ```json
        {
            "code": "xxx",  // error code
            "reason": "..." // error reason
        }
        ```
- **quittedRoom**: triggered when room has been left
    - event related to: `quitRoom()`
    - arguments:
        - room `JSON`
        ```json
        {
            "room": "aUpS45g7J0w" // room name
        }
        ```
- **quitRoomError**: triggered when an error occurred while quitting room
    - event related to: `quitRoom()`
    - arguments:
        - error `JSON`
        ```json
        {
            "code": "xxx",  // error code
            "reason": "..." // error reason
        }
        ```
- **peerJoinedRoom**: triggered when a new participant join the room
    - arguments:
        - peer `JSON`
        ```json
        {
            "room": "aUpS45g7J0w", // room name
            "name": "John Doe",    // remote peer name
            "pid": "rfpWEk"        // remote peer id
        }
        ```
- **peerQuittedRoom**: triggered when a new participant leave the room
    - arguments:
        - peer `JSON`
        ```json
        {
            "room": "aUpS45g7J0w", // room name
            "pid": "rfpWEk"        // remote peer id
        }
        ```
- **customSignal**: triggered when a custom signal is received
    - event related to: `sendCustomSignal()`
    - arguments:
        - data `JSON`

- **incomingRequest**: triggered when a remote user invite you to join a room
    - event related to: `call()`, `openDataChannel()`
    - arguments:
        - request `JSON`
        ```json
        {
            "room": "aUpS45g7J0w", // room name
            "pid": "rfpWEk",       // remote peer id
            "event": "call"        // request type "call" or "datachannel"
        }
        ```
- **incomingCall**: triggered if a new call or data channel request is received and `autoAcceptCall` option has been set to `false`
    - event related to: `call()`, `openDataChannel()`
    - arguments:
        - call `JSON`
        ```json
        {
            type: "media",           // call type (media, datachannel)
            pid: "rfpWEk",           // remote peer id
            room:  "aUpS45g7J0w",    // room name
            options: {…},        // call options
            client: {…},          // resquester's browser info
            sendonly: false,         // bidirectional call info
            accept: function (){…} // function to accept call
            reject: function (){…} // function to reject call
		
		}
        ```
- **callRejected**: triggered when a user reject an incoming call/data channel request
    - event related to: `call.reject()`
    - arguments:
        - pid `String` : peer id
        - room `String` : room name
    
- **callFailed**: triggered when a connection failed to be established between 2 participants
    - event related to: `call()`, `openDataChannel(), sendFile()`
    - arguments:
        - room `String`: room name
        - pid `String`: remote peer id
        - message `String`: error message
        - trace `Array`: peerConnection log
        - client `Object`: remote browser info
        ```json
        {
            "name": "chrome",        // browser name
            "version": "83",         // browser version
            "vendor": "google inc.", // browser vendor
            "os": "macos",           // os
            "osversion": "10.13.6"   // os version
        }
        ```
- **callEnded**: triggered when a call or a datachannel ends
    - event related to: `endCall()`, `endCalls()`
    - arguments:
        - pid `String` : remote peer id
        - room `String` : room name

- **presence**: triggered when a presence status is received
    - event related to: `getPresence()`
    - arguments:
        - presence `JSON` : remote peer id
        ```json
        {
            "id": "rfpWEk",      // peer id
            "presence": "online" // presence status
        }
        ```
---

## Streams Events

### bc.streams.bind ( event, handler )

Bind streams events.

Return a reference associated to the handler.

```js
const handler_ref = bc.streams.bind ( event, data => {} );
```

### bc.streams.unbind ( event, handler )

Unbind can be done using handler reference (remove a specific handler) or by an event name (remove all handlers)

```js
bc.streams.unbind ( handler_ref );

// or

bc.streams.unbind ( event );
```

### Events
- **streamMuteChange**: triggered when video mute status change
    - event related to: `muteVideo()`
    - arguments:
        - status `String`: mute status
        - pid `String`: remote peer id

- **streamAdded**: triggered when a new stream is available
    - event related to: `call()`, `updateCall()`
    - arguments:
        - stream `MediaStream`: remote stream
        - pid `String`: remote peer id

- **streamUpdated**: triggered when a stream has been updated
    - event related to: `call()`, `updateCall()`
    - arguments:
        - stream `MediaStream`: remote stream
        - pid `String`: remote peer id

- **streamClosed**: triggered when a stream has been closed
    - event related to: `endCall()`, `endCalls()`
    - arguments:
        - stream `MediaStream`: remote stream
        - pid `String`: remote peer id

- **trackAdded**: triggered when a new stream track has been added
    - event related to: `call()`, `updateCall()`
    - arguments:
        - track `MediaStreamTrack`: new remote audio/video track
        - stream `MediaStream`: remote stream
        - pid `String`: remote peer id

- **trackRemoved**: triggered when a stream track has been removed
    - event related to: `call()`, `updateCall()`
    - arguments:
        - track `MediaStreamTrack`: remote audio/video track removed
        - stream `MediaStream`: remote stream
        - pid `String`: remote peer id

- **streamStatsUpdated**: triggered when connection statistics have been updated
    - arguments:
        - stats `Object`: 
        ```json
        {
            "send": {…},       // sent data stats
            "recv": {…},       // received data stats
            "transport": {…},  // transport info
            "candidates": {…}, // ICE candidates info
            "client": {…}      // remote browser info
        }
        ```
        - pid `String`: remote peer id
        - room `String`: room name

- **devicesUpdated**: triggered when input/output devices list has been updated
    - arguments:
        - track `MediaStreamTrack`: remote audio/video track removed
        - stream `MediaStream`: remote stream
        - pid `String`: remote peer id
---

## Data Channel Events

### bc.channels.bind ( event, handler )

Bind data channel events.

Return a reference associated to the handler.

```js
const handler_ref = bc.channels.bind ( event, data => {} );
```

### bc.channels.unbind ( event, handler )

Unbind can be done using handler reference (remove a specific handler) or by an event name (remove all handlers)

```js
bc.channels.unbind ( handler_ref );

// or

bc.channels.unbind ( event );
```

### Events
- **dataChannelCreated**: triggered when a new data channel has been created on the caller side
    - event related to: `openDataChannel()`
    - arguments:
        - channel `DataChannel`: data channel object
        ```json
        {
            "label": "chat",                  // data channel label
            "send": function ( message ) {…}, // send message 
            "close": function () {…},         // close data channel
            "onOpen": undefined,              // "on open" callback
            "onMessage": undefined,           // "on new message" callback
            "onClose": undefined,             // "on close" callback
        }
        ```
        - pid `String`: remote peer id
- **dataChannelRequested**: triggered when a new data channel has been requested on the callee side
    - event related to: `openDataChannel()`
    - arguments:
        - channel `DataChannel`: data channel object
        ```json
        {
            "label": "chat",                  // data channel label
            "send": function ( message ) {…}, // send message 
            "close": function () {…},         // close data channel
            "onOpen": undefined,              // "on open" callback
            "onMessage": undefined,           // "on new message" callback
            "onClose": undefined,             // "on close" callback
        }
        ```
        - pid `String`: remote peer id

---

## File Transferts Events

### bc.files.bind ( event, handler )

Bind file transfert events.

Return a reference associated to the handler.

```js
const handler_ref = bc.files.bind ( event, data => {} );
```

### bc.channels.unbind ( event, handler )

Unbind can be done using handler reference (remove a specific handler) or by an event name (remove all handlers)

```js
bc.files.unbind ( handler_ref );

// or

bc.files.unbind ( event );
```

### Events
- **fileTransferWaiting**: triggered when a file transfert is created on the sender side
    - event related to: `sendFile()`
    - arguments:
        - channel `DataChannel`: data channel object
        - pid `String`: recipient id
        - file `Object`: file info
        ```js
            { 
                id: "iu798d4n",      // file transfert id
                name: "my_file.pdf", // file name
                size: "55.05 KB"     // file size
            }
        ```
    
- **newFileTransfer**: triggered when a file transfert request is received on the recipient side
    - event related to: `sendFile()`
    - arguments:
        - channel `DataChannel`: data channel object
        - pid `String`: sender id
        - file `Object`: file info
        ```js
            { 
                id: "iu798d4n",      // file transfert id
                name: "my_file.pdf", // file name
                size: "55.05 KB"     // file size
            }
        ```
        - answer `Object`: answer to file transfert request
        ```json
        {
            "ok": function () {…},    // accept file transfert request
            "cancel": function () {…} // reject file transfert request
        }
        ```
- **fileTransferProgress**: triggered periodically while the file transfert is in progress
    - event related to: `answer.ok()`
    - arguments:
        - id: `String`, file transfert uid
        - progression: `Number`, file transfert progression in %
- **fileTransferBitrate**: triggered periodically while the file transfert is in progress
    - event related to: `answer.ok()`
    - arguments:
        - id: `String`, file transfert uid
        - progression: `String`, file transfert progression in %
- **fileTransferAborted**: triggered when file transfert resquest has been rejected by the
 recipient
    - event related to: `answer.cancel()`
    - arguments:
        - id: `String`, file transfert uid

- **fileSent**: triggered when the file transfert is completed on the sender side
    - arguments:
        - id: `String`, file transfert uid

- **fileReceived**: triggered when the file transfert is completed on the recipient side
    - arguments:
        - id: `String`, file transfert uid
        - file `Object`: file info
        ```js
            { 
                id: "iu798d4n",      // file transfert id
                name: "my_file.pdf", // file name
                url: "…"             // file download url
            }
        ```