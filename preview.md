# Preview Embed

The preview embed allows you to add a live, fully functional, preview of your
embed to any page on your site.  The preview allows your users to try out or
configure your embed on their live site, no installation required.

## Getting Started

The preview tool is based around a script tag you add to your site.
Creating the tag wherever you'd like the preview to appear:

```html
<script src="https://preview.eager.io/embed.js" partner-id="MyApp"></script>

<script>
// This code need not be inline, it can be included with your general scripts.

(function(){
  // On every page previewed
  EagerPreview.on('navigate', function(){
    // Inject a script file
    EagerPreview.inject({
      type: 'application/javascript',
      src: '//myapp.com/embed.js'
    })
  })
})();
```

## API

Once you have added the preview script tag to your page, a global `EagerPreview` object
will be available.  The object has the following methods:

- `EagerPreview.inject(options)` - Inject HTML, CSS or Javascript to be executed in the
  previewed page.
- `EagerPreview.reload()` - Reload the previewed page
- `EagerPreview.on(eventName, handler)` - Bind an event handler (see the Events section)

## Injecting Your Code

Call `EagerPreview.inject` to inject code into the page being previewed.  For example:

```javascript
EagerPreview.inject({
  type: 'text/css',
  src: '//d2fsad3333d.cloudfront.net/my-app.css'
})
```

You can also specify the content to be injected directly, rather than using a file:


```javascript
EagerPreview.inject({
  type: 'application/javascript',
  content: 'MyApp.init()'
})
```

Finally, you can inject HTML.  Any script tags in the HTML will be automatically executed
once inserted:

```javascript
EagerPreview.inject({
  type: 'text/html',
  content: '<script type="text/javascript" src="https://myapp.com/embed.js" onload="MyApp.init()" data-user-id="5"></'+'script>'
})
```

Note that the `text/html` type does not support injection from files (`content` only, not `src`)

You can call `inject` at any time, if the page being injected into is not yet ready, the
request will be queued.

A call to `inject` only applies to the current page being viewed.  You generally want to
reinject your files after every `navigate` event (as is done in the example in the Getting
Started section).

### Supported Types

The following content types can be injected:

- `text/css`
- `application/javascript`
- `text/html` (`content` only, not `src`)

## Reload

The preferred method of reloading your embed when the user has changed options is to call
the appropriate javascript using `inject`.  If this is not possible however, you can reload
the whole page using `reload()`:

```javascript
EagerPreview.reload()
```

## Events

The Eager Preview fires several events. Most critically, the `navigate` event is fired on
initial preview load and whenever the user navigates to a new page within the preview.  As
in the Getting Started example, it is important to inject your scripts whenever navigate fires
to ensure your embed appears on every page the user visits.

## Reference

### Attributes

The following attributes may be set on the embed.js script tag:

- `partner-id` (required) - The Partner ID provided to you by Eager

### JS API

The `EagerPreview` object will be added to the window by the embed.js
script tag.

#### Methods

- `inject({options})` - Inject the provided CSS, JS or HTML into the page being previewed.  Note that you
must reinject after the `navigate` event if you wish your files to appear on every page the user previews.
- `reload()` - Reload the page being previewed, optionally specifying a new list of files to be injected
- `on(eventName, handler)` - Bind a handler to one of the [supported events](#events)
- `once(eventName, handler)` - Bind a handler to just the next firing of one of the [supported events](#events)
- `off(eventName, [handler])` - Unbind an event handler

#### Events

- `navigate` - The user has navigated to a new page by following a link, entering a URL manually,
  or through JS on the previewed page.  This event also fires after the initial page load.
- `load` - The preview has been loaded
- `error` - An error has occured during preview loading.  We will show an error message, but error information
is also provided with this event.
