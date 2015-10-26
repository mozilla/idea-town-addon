#Idea Town DEPRECATED see [idea-town repo](https://github.com/mozilla/idea-town/tree/master/addon)
The addon where ideas come to idea

## installation

`npm install`

## configuration

check the `CONFIG` property in `package.json`

## running

* `npm start`
* `jpm run -b /usr/bin/firefox`

## packaging

`npm run package`

## Events

Accepted:
* `update-check`
* `update-approve`
* `loaded`

Emitted:
* `addon-available`
* `addon-updates`
* `addon-install:install-started`
* `addon-install:install-new`
* `addon-install:install-cancelled`
* `addon-install:install-ended`
* `addon-install:install-failed`
* `addon-install:download-started`
* `addon-install:download-progress`
* `addon-install:download-ended`
* `addon-install:download-cancelled`
* `addon-install:download-failed`
* `addon-self:installed`
* `addon-self:enabled`
* `addon-self:upgraded`

Any emitted events prefixed with `addon-install:` will have an associated object
which will be structured as such:

``` json
{
  "name": "Fox Splitter",
  "error": 0,
  "state": 6,
  "version": "2.1.2012122901.1-signed",
  "progress": 517308,
  "maxProgress": 517308
}

```
The event `addon-install:install-ended` will include some extra properties:

``` json
{
  "name": "Fox Splitter",
  "error": 0,
  "state": 6,
  "version": "2.1.2012122901.1-signed",
  "progress": 517308,
  "maxProgress": 517308,
  "description": "Splits browser window as you like.",
  "homepageURL": "http://piro.sakura.ne.jp/xul/foxsplitter/index.html.en",
  "iconURL": "file:///tmp/074a4b62-239e-49bb-b75a-4935c349855c/extensions/foxsplitter@piro.sakura.ne.jp/icon.png",
  "size": 511221,
  "signedState": 2,
  "permissions": 13
}
```

#### Talking to the addon

You will need to setup the following function (or an equivalent) to send messages to the addon.

``` javascript
function sendToAddon (data) {
  document.documentElement.dispatchEvent(new CustomEvent(
    'from-web-to-addon', { bubbles: true, detail: data }
  ));
}
```
Then you can use the `sendToAddon` method to send messages.

``` javascript
sendToAddon({type: 'update-check'});
```
and to setup listeners.

``` javascript
window.addEventListener("from-addon-to-web", function (event) {
  if (!event.detail || !event.detail.type) { return; }
  statusUpdate(event.detail.type, event.detail);
  switch (event.detail.type) {
    case 'addon-available':
      handleWhenAddonAvailable(event.detail);
      break;
    case 'addon-updates':
      renderUpdates(event.detail);
      break;
    default:
      console.log('WEB RECEIVED FROM ADDON', JSON.stringify(event.detail, null, ' '));
      break;
  }
}, false);
```

Submit updates:
``` javascript
sendToAddon({type: 'update-approve', detail: approvedUpdates});
```

`approvedUpdates` is an array of addon names.
This will trigger the download/install process.

find more examples in [stub-content/index.js](./stub-content/index.js).

## Maintainers

* Dave Justice <djustice@mozilla.com
* Les Orchard <lorchard@mozilla.com>
