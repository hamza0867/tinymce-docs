### `tinycomments_can_delete_comment`

_Optional_ - This option sets the author permissions for _deleting comments_. If the `tinycomments_can_delete_comment` option is not included, the current author (`tinycomments_author`) cannot delete comments added by other authors.

**Type:** `Function`

**Default Function:**

```js
function (req, done, fail) {
  var allowed = req.comment.author === <Current_tinycomments_author>;
  done({
    canDelete: allowed
  });
}
```

The following example extends the default behavior to allow the author `<Admin user>` to delete other author's comments by adding `|| currentAuthor === '<Admin user>'`.

#### Example: Using `tinycomments_can_delete_comment`

```js
var currentAuthor = 'embedded_journalist';

tinymce.init({
  selector: '#textarea',
  tinycomments_author: currentAuthor,
  tinycomments_can_delete_comment: function (req, done, fail) {
    var allowed = req.comment.author === currentAuthor;
    done({
      canDelete: allowed || currentAuthor === '<Admin user>'
    });
  }
});
```

