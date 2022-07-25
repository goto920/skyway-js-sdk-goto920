# setBandwidth() (Experimental project)

## MediaConnection.setBandwidth({audio: audioKbps, video: videoKbps})
        - to change max kbps during a session

## Modified files for setBandwidth()
- src/peer/mediaConnection.js
- src/peer/negotiator.js

## The code (mediaConnection.js)
```
  setBandwidth(newBandwidth) {
    if (!this.open) return;
    this._negotiator.setBandwidth(newBandwidth);
  }

```
## The code (negotiator.js)
```
 async setBandwidth(newBandwidth) {
    try {
      this._audioBandwidth = newBandwidth.audio;
      this._videoBandwidth = newBandwidth.video;

      const offer = await this._makeOfferSdp();
      await this._setLocalDescription(offer);
      this._isNegotiationAllowed = true;
      this.emit(Negotiator.EVENTS.negotiationNeeded.key);
    } catch (e) {
      logger.error(e);
    }
  }

```
