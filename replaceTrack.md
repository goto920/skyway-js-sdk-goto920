# replateTrack() (Experimental project, not tested yet)

## MediaConnection.replace(oldTrack, newTrack)
        - replace audio/video track during a session

## Modified files for addTrack()
- src/peer/mediaConnection.js
- src/peer/negotiator.js

## The code (mediaConnection.js)
```
 replaceTrack(oldTrack, newTrack) {
    if (!this.open) return;
    this._negotiator.replaceTrack(oldTrack, newTrack);
  }

```
## The code (negotiator.js)
- check for kind of track (audio or video) needed?
```
  replaceTrack(oldTrack, newTrack) {
    //logger.log('negotiator replaceTrack() called, pc', this._pc);
    try {
      const sender = this._pc
        .getSenders()
        .find(s => s.track.id === oldTrack.id);
      if (sender) sender.replaceTrack(newTrack);
    } catch (e) {
      logger.error(e);
    }
  }

```
