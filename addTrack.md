# addTrack() (Experimental project)

## MediaConnection.addTrack(track)
        - add audio/video track during a session

## Modified files for addTrack()
- src/peer/mediaConnection.js
- src/peer/negotiator.js

## The code (mediaConnection.js)
```
  addTrack(newTrack) {
    if (!this.open) return;
    try {
      if (this.localStream)
        this._negotiator.addTrack(newTrack, this.localStream);
    } catch (e) {
      logger.error(e);
    }
  }
```
## The code (negotiator.js)
```
  async addTrack(newTrack, stream) {
    try {
      this._pc.addTrack(newTrack, stream);
      const offer = await this._makeOfferSdp();
      await this._setLocalDescription(offer);
      this._isNegotiationAllowed = true;
      this.emit(Negotiator.EVENTS.negotiationNeeded.key);
    } catch (e) {
      logger.error(e);
    }
    return;
  }

```
