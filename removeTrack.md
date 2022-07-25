# removeTrack() (Experimental project)

## MediaConnection.removeTrack(track)
        - remove audio/video track during a session
	- search the sender by track.id and remove the sender
	- try to remove inactive senders (mmm... sender cannot be removed)

## Modified files for removeTrack()
- src/peer/mediaConnection.js
- src/peer/negotiator.js

## The code (mediaConnection.js)
```
  removeTrack(currentTrack) {
    logger.warn('KG mediaConnection removeTrack()');
    if (!this.open) return;
    this._negotiator.removeTrack(currentTrack);
  }
```
## The code (negotiator.js)
```
  async removeTrack(currentTrack) {
    logger.warn('KG NEGOTOR removeTrack() called, pc', this._pc);
    try {
      const senders = this._pc.getSenders();

      senders.forEach(s => {
        if (s.track) {
          if (s.track.id === currentTrack.id) {
            logger.warn('removed currentTrack, sender', currentTrack, s);
            this._pc.removeTrack(s);
            s.track.stop();
          } 
          logger.warn('removed currentTrack, sender not found', 
            currentTrack.id, s.track.id);
        } else {
          logger.warn('removed sender without track', s);
          this._pc.removeTrack(s);
          s.track.stop();
        }
      });
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
