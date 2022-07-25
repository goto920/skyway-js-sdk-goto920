# Adding some functionalities to SkyWay JS SDK (Experimental project)

[original README.md](./README-original.md)

## Modify opus parameters in SDP Offer/Answer (update: July 26, 2022)
	- Reason 1: Chrome does not set stereo=1
	- Reason 2: set ptime to reduce encoding latency (in experiment)
	- Reason 3: use cbr (constant bit rate)
- setOpusConfig(sdp,maxbps) (shared/sdpUtils.js)
	- called from Negotiator._makeOfferSdp() - no user interaction
	- maxbps is set in SkyWay audioBandwidth option or 
	- MediaConnection.setBandwidth() (new method below)
```
fmtp for opus
 'sprop-stereo=1;stereo=1;useinbandfec=1;cbr=1;'
 +`maxaveragebitrate=${maxbps}`; // especially for sender in Firefox
m.ptime = '3'; // min in RFC (3,5,10,20,....)
m.maxptime = '20'; // default in RFC (crashes if included in fmtp)

```
details: [setOpusConfig.md](./setOpusConfig.md)

## Added methods to MediaConnection (part of the API)

- MediaConnection.setBandwidth({audio: audioKbps, video: videoKbps})
	- to change max kbps during a session
	- details: [setBandwidth.md](./setBandwidth.md)
- MediaConnection.addTrack(newTrack)
	- to add a track during a session
	- details: [addTrack.md](./addTrack.md)
- MediaConnection.removeTrack(track)
	- to remove a track during a session
	- details: [removeTrack.md](./removeTrack.md)
	- Note: The track cannot be really removed from the receiving stream. RTP transmission with 0 bps stays alive.
- MediaConnection.replateTrack(oldTrack, newTrack) // not tested yet
	- to replace same kind of track
	- details: [replaceTrack.md](./replaceTrack.md)

## Modified files
- src/shared/sdpUtils.js
- src/peer/mediaConnection.js
- src/peer/negotiator.js

## Build
Follow the instruction in the original README.md.
- Note: Use LTS version of Node.js (tested with v16.15.1)
	- node/18.2.0 did not work for me
- npm install
- npm run lint; npm run test
- npm run build
- use dist/skyway.js or skyway.min.js for your project

## Use cases

```
// GLOBAL.mediaConnection = mediaConnection; 
// when mediaConnection established
//   if (GLOBAL.mediaConnection && GLOBAL.mediaConnection.open)

    // Receiving side
    GLOBAL.mediaConnection.setBandwidth({audio: 256, video: 500);
    // max 510 for audio (falls back to 64 kbps if lager value is set)

    // Sending side
    GLOBAL.mediaConnection.addTrack(newTrack); // video or audio

    GLOBAL.mediaConnection.removeTrack(trackToBeRemoved); // video or audio

    GLOBAL.mediaConnection.replaceTrack(old, new); // not tested

```
###How to detect addTrack/removeTrack at receiving side


```
// This event fires only when a mediaConnection is established
   mediaConnection.on('stream', (stream) => { // Caller and Callee

   stream.onaddtrack = e => {
      playReceivingStream(stream); 
   }

   stream.onremovetrack = e => {
      playReceivingStream(stream);
   }

```
Note: Removed senders (RTCPeerConnection.remoteTrack(sender)) does not stop sending track but in 0 kbps. It is in investigation.
