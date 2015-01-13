# `rtcninja.Connection` Class API

The `rtcninja.Connection` class wrappes a native `(webkit|moz)RTCPeerConnection` and attempts to provide a uniform behavior across different WebRTC implementations. The provides API mimics the `RTCPeerConnection` API in the latest [WebRTC draft](http://w3c.github.io/webrtc-pc/).


## Features

* Normalization of the `pcConfig.iceServers` field (it deals with both the deprecated `url` field and the new `urls` one).
* Filtering of certain events once the `RTCPeerConnection` is closed. For example, old versions of Firefox fire `onicecandidate` or `onaddstream` events even after `close()` is called.
* Filtering of duplicate events. For example, some old browsers fire consecutive `oniceconnectionstatechange` events having the `iceConnectionState` attribute the same value in them.
* Normalization of the ICE candidate syntax. Within the `onicecandidate` event of some old browsers the `candidate.candidate` field contains incorrect "a=" and "\r\n".
* Implementing the `pcConfig.iceTransports` field in browsers not supporting it.


## `new rtcninja.Connection(pcConfig, constraints)` constructor


### `pcConfig` {Object} param

Mandatory Object holding the [`RTCConfiguration`](http://w3c.github.io/webrtc-pc/#idl-def-RTCConfiguration) dictionary of the `RTCPeerConnection`. Some features not implemented in some browsers (such a Firefox) are implemented in JavaScript (such as `iceTransports`).

There are also custom options not present in the WebRTC specification:

* `gatheringTimeout` {Number}: ICE gathering is terminated after the given time (milliseconds) and a faked `onicecandidate` event with `candidate = null` is fired. No more `onicecandidate` events are fired once this timeout.
* `gatheringTimeoutAfterRelay` {Number}: Once the first "relay" (TURN) candidate is gathered, ICE gathering is terminated after the given time (milliseconds) and a faked `onicecandidate` event with `candidate = null` is fired. No more `onicecandidate` events are fired once this timeout.


### `constraints` {Object} param

`constraints` is an optional Object. Note however that this second argument is deprecated in the latest versions of the WebRTC draft (RTC constrains are now set in `getUserMedia()`, `RTCPeerConnection.createOffer()` and `RTCPeerConnection.createAnswer()`).


## Other API

`rtcninja.Connection` provides the same functions and attributes as described in the latest WebRTC draft. There are a few exceptions:

### Events

All the native `RTCPeerConnection` events are fired with a single `event` argument of type `Event`. Events in `rtcninja.Connection` are full compatible with the original ones, but some of them also provide a second argument:

* `onicecandidate(event, candidate)`: Second argument is the normalized `event.candidate` field.
* `onaddstream(event, stream)`
* `onremovestream(event, stream)`
* `ondatachannel(event, channel)`
* `onsignalingstatechange(event, signalingState)`
* `oniceconnectionstatechange(event, iceConnectionState)`
* `onicegatheringstatechange(event, iceGatheringState)`
