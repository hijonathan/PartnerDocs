# Preview Embed

The preview embed allows you to add a live, fully functional, preview of your
embed to any page on your site.  The preview allows your users to try out or
configure your embed on their live site, no installation required.

## Getting Started

The first step is to include the embed.js script in your app:

```html
<script src="https://preview.eager.io/embed.js"></script>
```

Next, we create an instance of the EagerPreview object, providing it with
an element on the page for the preview to be rendered into:

```javascript
var preview = new window.EagerPreview({
  partnerId: 'my-partner-id',
  el: document.querySelector('#my-preview-container')
})
```

Finally we render the preview:

```javascript
preview.render()
```

## API

Your instance of `EagerPreview` has the following methods:

- `preview.inject(options)` - Inject HTML, CSS or Javascript to be executed in the
  previewed page.
- `preview.navigate(url)` - Navigate the previewed page to the specified URL
- `preview.reload()` - Reload the previewed page
- `preview.on(eventName, handler)` - Bind an event handler (see the Events section)

## Injecting Your Code

Assuming `preview` is your `EagerPreview` instance, call `preview.inject` to inject
code into the page being previewed.  For example:

```javascript
preview.inject({
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
reinject your files after every `navigate` event:

```javascript
var inject = function(){
  preview.inject({
    type: 'application/javascript',
    src: '//myapp.com/embed.js'
  })
}

// On every page previewed
preview.on('navigate', inject);
```

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
preview.reload()
```

## Events

The Eager Preview fires several events. Most critically, the `navigate` event is fired on
initial preview load and whenever the user navigates to a new page within the preview.  As
in the Getting Started example, it is important to inject your scripts whenever navigate fires
to ensure your embed appears on every page the user visits.

## Reference

### JS API

The `EagerPreview` class will be added to the window by the embed.js
script tag.

#### Methods

- `new EagerPreview({options})` - Construct a new preview object.  See [Options](#Options) below.
- `render()` - Render the preview iframe into the specified container.  Must be called for the preview to be visible.
- `inject({options})` - Inject the provided CSS, JS or HTML into the page being previewed.  Note that you
must reinject after the `navigate` event if you wish your files to appear on every page the user previews.
- `navigate(url)` - Navigate the previewed page to the specified URL
- `reload()` - Reload the page being previewed, optionally specifying a new list of files to be injected
- `navigate(url)` - Navigate the preview to the specified url
- `on(eventName, handler)` - Bind a handler to one of the [supported events](#events)
- `once(eventName, handler)` - Bind a handler to just the next firing of one of the [supported events](#events)
- `off(eventName, [handler])` - Unbind an event handler

#### Options

The constructor accepts the following options:

- `partnerId` (string) - required - The partner id provided to you by Eager
- `el` (DOM Node) - required - The element you would like the preview rendered into.  It's contents will be replaced.
- `frameSrc` (string) - optional - The initial URL to navigate the preview to
- `showURLBar` (bool) - optional, default: true - Should the URL bar be shown at the top of the preview?

#### Events

The following events are triggered on the preview object:

- `navigate` - The user has navigated to a new page by following a link, entering a URL manually,
  or through JS on the previewed page.  This event also fires after the initial page load.
- `load` - The preview has been loaded
- `error` - An error has occured during preview loading.  We will show an error message, but error information
is also provided with this event.
