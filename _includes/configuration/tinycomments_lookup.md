### `tinycomments_lookup`

The Comments plugin uses the `tinycomments_lookup` function to retrieve an existing conversation using a conversation's unique ID.

The **Display names** configuration must be considered for the `tinycomments_lookup` function:

Display names
: The Comments plugin uses a simple string for the display name. For the `lookup` function, Comments expects each comment to contain the author's display name, not a user ID, as Comments does not know the user identities. The `lookup` function should be implemented considering this and resolve user identifiers to an appropriate display name.

The conventional conversation object structure that should be returned via the `done` callback is as follows:

The `tinycomments_lookup` function is passed a (`req`) object as the first parameter, which contains the following key-value pair:

`conversationUid`
: The uid of the conversation the reply is targeting.

The `done` callback should accept the following object:

```js
{
 conversation: {
   uid: string, // the uid of the conversation,
   comments: [
    {
      author: string, // author of first comment
      authorName: string, // optional - Display name to use instead of author. Defaults to using `author` if not specified
      createdAt: date, // when the first comment was created
      content: string, // content of first comment
      modifiedAt: date, // when the first comment was created/last updated
      uid: string // the uid of the first comment in the conversation
    },
    {
      author: string, // author of second comment
      authorName: string, // optional - Display name to use instead of author. Defaults to using `author` if not specified
      createdAt: date, // when the second comment was created
      content: string, // content of second comment
      modifiedAt: date, // when the second comment was created/last updated
      uid: string // the uid of the second comment in the conversation
    }
  ]
 }
}
```

> **Note**: The dates should use [ISO 8601 format](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString). This can be generated in JavaScript with: `new Date().toISOString()`.

For example:

```js
function lookup_comment({ conversationUid }, done, fail) {
  let lookup = async function () {
    let convResp = await fetch(
      'https://api.example/conversations/' + conversationUid
    );
    if (!convResp.ok) {
      throw new Error('Failed to get conversation');
    }
    let comments = await convResp.json();
    let usersResp = await fetch('https://api.example/users/');
    if (!usersResp.ok) {
      throw new Error('Failed to get users');
    }
    let { users } = await usersResp.json();
    let getUser = function (userId) {
      return users.find((u) => {
        return u.id === userId;
      });
    };
    return {
      conversation: {
        uid: conversationUid,
        comments: comments.map((comment) => {
          return {
            ...comment,
            content: comment.content,
            authorName: getUser(comment.author)?.displayName,
          };
        }),
      },
    };
  };
  lookup()
    .then((data) => {
      console.log('Lookup success ' + conversationUid, data);
      done(data);
    })
    .catch((err) => {
      console.error('Lookup failure ' + conversationUid, err);
      fail(err);
    });
}

tinymce.init({
  selector: '#editor',
  plugins: 'tinycomments',
  tinycomments_mode: 'callback',
  tinycomments_create: create_comment,
  tinycomments_reply: reply_comment,
  tinycomments_edit_comment: edit_comment,
  tinycomments_delete: delete_comment_thread,
  tinycomments_delete_all: delete_all_comment_threads,
  tinycomments_delete_comment: delete_comment,
  tinycomments_lookup: lookup_comment // Add the callback to TinyMCE
});
```

