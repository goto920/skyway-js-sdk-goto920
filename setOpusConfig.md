# setOpusConfig() (Experimental project)

[original README.md](./README-original.md)

## Modify opus parameters in SDP Offer/Answer (update: July 26, 2022)
	- Reason 1: Chrome does not set stereo=1
	- Reason 2: set ptime to reduce encoding latency (in experiment)
	- Reason 3: use cbr (constant bit rate)
- setOpusConfig(sdp,maxbps) (shared/sdpUtils.js)
	- called from Negotiator._makeOfferSdp() - no user interaction
	- maxbps is set in SkyWay audioBandwidth option or 
	- MediaConnection.setBandwidth() (added by KG)
## Modified files for setOpusConfig()
- src/shared/sdpUtils.js
- src/peer/negotiator.js

## The code (negotiator.js)
```
  async _makeOfferSdp() {
  // (omitted)
    offer.sdp = sdpUtil.setOpusConfig(offer.sdp,this._audioBandwidth); 
     // added by KG (just before return)

    return offer;
  }

  async _makeAnswerSdp() {

  // (omitted)
    answer.sdp = sdpUtil.setOpusConfig(answer.sdp,this._audioBandwidth); 
      // added by KG (just before setLocalDescription()

    try {
      await this._pc.setLocalDescription(answer);

  }

```
## The code (sdpUtils.js)
```
  setOpusConfig(sdp,maxkbps) {
    const maxbps = (maxkbps*1000).toString();
    const res = sdpTransform.parse(sdp);
    const audioMedia = res.media.filter(m => m.type === 'audio');

    // find fmtp line for opus in each m= block)
    audioMedia.forEach(m => {
      const opusRtp = m.rtp.filter(rtp => rtp.codec === 'opus');
        logger.warn('OpusRTP\n', JSON.stringify(opusRtp));
      opusRtp.forEach(rtp => {
        const opusFmtp = m.fmtp.find(fmtp => fmtp.payload === rtp.payload);
        logger.warn('opusFmtp before\n', JSON.stringify(opusFmtp));
        if (opusFmtp) {
          opusFmtp.config 
        = 'sprop-stereo=1;stereo=1;useinbandfec=1;cbr=1;'
          +`maxaveragebitrate=${maxbps}`; 
          // especially for Firefox, which ignores TIAS for audio
          m.ptime = '3'; // min in RFC (3,5,10,20,....)
          m.maxptime = '20'; // default in RFC
        }
        logger.warn('m=audio After\n', JSON.stringify(m));
      });
    });
    return sdpTransform.write(res);
  }
  /* End added by KG */

```
