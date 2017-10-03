# node-mattermost

A node module for sending and receiving messages with Mattermost via webhooks.

[Mattermost](http://www.mattermost.org/) is a messaging platform that is easy to integrate with.
This module should be useful for creating various integrations with Mattermost, such as
chat bots!

## Install Mattermost

node-mattermost is available via npm:

```
npm install node-mattermost
```


Get your hook_url from the Mattermost Incoming Webhooks Integration page.

```
var Mattermost = require('node-mattermost');
var mattermost = new Mattermost(hook_url,options);
```

If your system requires that requests be made through
an HTTP or HTTPS proxy, you can either set an environment
variables https_proxy and http_proxy,
or pass in the optional third option:

```
var mattermost = new Mattermost(hook_url,{proxy: http_proxy});
```

The options object otherwise follows the options object of [request](https://github.com/request/request).
The url option will be set to the hook_url, and the json option will be set to the first object passed to mattermost.send


To send a message, call mattermost.send:

```
mattermost.send({
	text: 'Howdy!',
	channel: '#foo',
	username: 'Bot'
});
```

You can also specify an emoji icon, a url to a custom icon, attachments,
and any of the other options listed [here](http://docs.mattermost.com/developer/webhooks-incoming.html).


```
mattermost.send({
	text: 'Howdy!',
	channel: '#foo',
	username: 'Bot',
	icon_url: 'http://www.foo.com/image.png',
	icon_emoji: 'taco',
	attachments: attachment_array,
	unfurl_links: true,
	link_names: 1
});
```



To respond to an outgoing webhook from mattermost, pass the information from the webhook into mattermost.respond,
along with a callback function responsible for returning a response.

From inside an Express.js route, this is as easy as passing in req.body:

```
var express = require('express');
var app = express();
var bodyParser = require('body-parser');

var Mattermost = require('node-mattermost');
var mattermost = new Mattermost();

app.use(bodyParser.urlencoded({ extended: true }));

app.post('/', function(req, res) {

    var reply = mattermost.respond(req.body, function(hook) {

        return {
            text: 'Good point, ' + hook.user_name,
            username: 'Bot'
        };

    });

    res.json(reply);
});

app.listen(8080);
```

### Notes

Based on [node-slack](https://github.com/xoxco/node-slack)
