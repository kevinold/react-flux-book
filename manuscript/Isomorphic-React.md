# Isomorphic React

Below is code that shows how to to render your React app server side using [hapi](http://hapijs.com/).

```js
var Hapi = require('hapi');
var Good = require('good');
var React = require('react/addons');

require('node-jsx').install();  // node-jsx required to compile your app

var server = new Hapi.Server();

var ReactApp = React.createFactory(require('./src/js/components/App.react'));

server.connection({ host: 'localhost', port: 5001, routes: { cors: true } });

server.views({
    engines: {
        html: require('swig')
    },
    relativeTo: __dirname,
    path: './src/views'
});

server.route({
    method: 'GET',
    path: '/',
    handler: function (request, reply) {
        var reactHtml = React.renderToString(ReactApp({}));
        console.log(reactHtml);
        reply.view('index', { reactHtml: reactHtml });
    }
});

// Statically serve 'dist' directory
server.route({
    method: 'GET',
    path: '/{param*}',
    handler: {
        directory: {
            path: 'dist'
        }
    }
});

server.register({
  register: Good,
  options: {
    reporters: [{
      reporter: require('good-console'),
      args: [{log: '*', response: '*'}]
    }]
  }
}, function(err){
  if(err) {
    throw err; 
  }

  server.start(function () {
    console.log('Server running at:', server.info.uri);
  });
});
```

Line 25 would result in the following image:

(Screenshot of Chrome HTML console showing React DOM prerendered)
