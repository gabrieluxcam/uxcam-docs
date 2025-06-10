---
title: Example
deprecated: false
hidden: false
metadata:
  robots: index
---
## Example Initialisation with Configuration

Here is an example of initialising the SDK with an application version and occlusion settings:

```javascript
<script type="text/javascript" defer="">
(function(appKey, opts) {
    window.uxc = {
        __t: [],
        __ak: appKey,
        __o: opts,
        event: function(n, p) {
            this.__t.push(['event', n, p]);
        },
        setUserIdentity: function(i) {
            this.__t.push(['setUserIdentity', i]);
        },
        setUserProperty: function(k, v) {
            this.__t.push(['setUserProperty', k, v]);
        },
        setUserProperties: function(p) {
            this.__t.push(['setUserProperties', p]);
        },
    };
    var head = document.getElementsByTagName('head')[0];
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = '//websdk-recording.uxcam.com/index.js';
    script.async = true;
    script.defer = true;
    script.id = 'uxcam-web-sdk';
    script.crossOrigin = 'anonymous';
    head.appendChild(script);
})('APP_KEY', {
    appVersion: '1.0.0',
    occlusion: {
        queryParams: ['product', 'userId'],
        url: function(url) {
            // Custom URL occlusion logic
            return url.replace(/\/invite\/\w+/, '/invite/:inviteId');
        }
    }
});
</script>
```

##