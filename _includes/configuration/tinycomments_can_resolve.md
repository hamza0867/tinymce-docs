### `tinycomments_can_resolve`

{{site.requires_5_8v}}

_Optional_ - This option adds a _Resolve Conversation_ item to the dropdown menu of the first comment in a conversation. This callback sets the author permissions for _resolving comment conversations_.

**Type:** `Function`

#### Example: Using `tinycomments_can_resolve`

```js
var currentAuthor = 'embedded_journalist';

tinymce.init({
  selector: '#textarea',
  tinycomments_author: currentAuthor,
  tinycomments_can_resolve: function (req, done, fail) {
    var allowed = req.comments.length > 0 &&
                  req.comments[0].author === currentAuthor;
    done({
      canResolve: allowed || currentAuthor === '<Admin user>'
    });
  }
});
```

