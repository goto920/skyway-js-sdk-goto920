# Adding some functionalities to SkyWay JS SDK

[original README.md](./README-original.md)

## Modify opus parameters in SDP Offer/Answer

- setOpusConfig(sdp) (shared/sdpUtils.js)
	- called from Negotiator._makeOfferSdp()
		- no user interaction
	- 'maxptime=10;stereo=1;useinbandfec=1' (fixed parameters)
	- Reason 1: Chrome does not set stereo=1
	- Reason 2: set maxptime to reduce encoding latency (in experiment)

## Added methods to MediaConnection (part of the API)

- MediaConnection.setBandwidth({audio: audioKbps, video: videoKbps})
	- to change max kbps during a session
- MediaConnection.addTrack(newTrack)
	- to add a track during a session
- MediaConnection.removeTrack(track)
	- to remove a track during a session
- MediaConnection.replateTrack(oldTrack, newTrack) // not tested yet
	- to replace same kind of track

## Modified files
- src/shared/sdpUtils.js
- src/peer/mediaConnection.js
- src/peer/negotiator.js

## build
Follow the instruction in the original README.md.
- npm install; npm run build
- Note: Use LTS version of Node.js (tested with v16.15.1)
	- node/18.2.0 did not work for me


