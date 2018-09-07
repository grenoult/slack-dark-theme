# slack-dark-theme
Dark theme for Slack

# Instructions

1 - Find your Slack's application directory:
 - Windows: %homepath%\AppData\Local\slack\
 - Mac: /Applications/Slack.app/Contents/
 - Linux: /usr/lib/slack/ (Debian-based)
 
2 - Open app-X.X.X\resources\app.asar.unpacked\src\static\ssb-interop.js

3 - At the bottom, add:
 
```
// First make sure the wrapper app is loaded
document.addEventListener("DOMContentLoaded", function() {

// Then get its webviews
let webviews = document.querySelectorAll(".TeamView webview");

// Fetch our CSS in parallel ahead of time
const cssPath = 'https://raw.githubusercontent.com/grenoult/slack-dark-theme/master/dark-theme.css';
let cssPromise = fetch(cssPath).then(response => response.text());

let customCustomCSS = ``; // Add custom css here

// Insert a style tag into the wrapper view
cssPromise.then(css => {
    let s = document.createElement('style');
    s.type = 'text/css';
    s.innerHTML = css + customCustomCSS;
    document.head.appendChild(s);
});

// Wait for each webview to load
webviews.forEach(webview => {
    webview.addEventListener('ipc-message', message => {
      if (message.channel == 'didFinishLoading')
          // Finally add the CSS into the webview
          cssPromise.then(css => {
            let script = `
                  let s = document.createElement('style');
                  s.type = 'text/css';
                  s.id = 'slack-custom-css';
                  s.innerHTML = \`${css + customCustomCSS}\`;
                  document.head.appendChild(s);
                  `
            webview.executeJavaScript(script);
          })
    });
});
});
```

4 - Restart Slack

5 - If you're not happy with colors, you can change them using this link: https://github.com/widget-/slack-black-theme#color-schemes

Sources:
 - https://github.com/widget-/slack-black-theme
 - https://github.com/widget-/slack-black-theme/issues/48#issuecomment-382017521
