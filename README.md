ShareJS
=======

This is a little server (& client library) to allow concurrent editing of any kind of content. The server runs on NodeJS and the client works in NodeJS or a web browser.

At the moment only plain-text is supported, but JSON and rich text should be supported soon.

Check out [some cool demos](http://sharejs.org:8000/)


Installing and running
----------------------

    npm install share

Run the examples with:

    # sharejs-exampleserver

### From source

    # git clone git://github.com/josephg/ShareJS.git
    # cd ShareJS

Run the tests:

    # cake test

Build the coffeescript into .js:

    # cake build
    # cake webclient

Run the example server:

    # bin/exampleserver

Running a server
----------------

There are two ways to run a sharejs server:

1. Run from a node.js app:

        var connect = require('connect'),
            sharejs = require('share').server;

        var server = connect(connect.logger());
        var options = {db: {type: 'memory'}}; // See docs for options.

        // Attach the sharejs REST and Socket.io interfaces to the server
        sharejs.attach(server, options);

        server.listen(8000);
        console.log('Server running at http://127.0.0.1:8000/');

2. From the command line:

        # sharejs

  Configuration is pulled from a configuration file that can't be easily edited at the moment. For now, I recommend method #1 above.

3. If you are just mucking around, run:

        # sharejs-exampleserver
  
  This will run a simple server on port 8000, and host all the example code there. Run it and check out `http://localhost:8000/`. The server will just store everything in ram, so don't get too attached to your data.


Putting Share.js on your website
--------------------------------

If you want to get a simple editor working in your webpage with sharejs, here's what you need to do:

First, get an ace editor on your page:

    <div id="editor"></div>

Your web app will need access to the following JS files:

- Ace (http://ace.ajax.org/)
- SocketIO (http://socket.io/).
- ShareJS client and ace bindings.

Add these script tags:

    <script src="http://ajaxorg.github.com/ace/build/src/ace.js"></script>
	<script src="/socket.io/socket.io.js"></script>
	<script src="/share/share.js"></script>
	<script src="/share/share-ace.js"></script>

And add this code:

    <script>
        var editor = ace.edit("editor");

        sharejs.open('hello', 'text', {host: 'localhost', port: 8000}, function(doc, error) {
	        doc.attach_ace(editor);
        });
	</script>

Thats about it :)

The hostname and port are not needed if you're hosting your webpage from the same host & port as sharejs.

Have a look at the examples in the repository for demos. If you want to see this examples running, run `# sharejs-exampleserver` and browse to http://localhost:8000/ .


Writing a client using node.js
------------------------------

The client API is the same whether you're using the web or nodejs.

Here's an example application which opens a document and inserts some text in it. Every time an op is applied to the document, it'll print out the document's version.

Run this from a couple terminal windows when sharejs is running to see it go.

    var client = require('share').client;

    // Open the 'hello' document, which should have type 'text':
    client.open('hello', 'text', {host: 'localhost', port: 8000}, function(doc, error) {
        // Insert some text at the start of the document (position 0):
        doc.submitOp({i:"Hi there!\n", p:0});

        // Get the contents of the document for some reason:
        console.log(doc.snapshot);

        doc.on('change', function(op) {
            console.log('Version: ' + doc.version);
        });

        // Close the doc if you want your node app to exit cleanly
	    // doc.close();
    });

See [`doc/client.md`](ShareJS/blob/master/doc/client.md) for full API documentation, and `examples/node*` for some more example apps.

