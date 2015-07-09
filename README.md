
Thundermole
===========

Proxy or redirect requests to different applications based on an API response.

![Thundermole Flow Diagram](docs/Diagram.png?raw=true)


Installing
----------

- Install [Node.js][node]
- Clone this repo locally and `cd` into it
- Copy the sample routes with `cp config/routes.sample.json config/routes.json`
- Optionally copy the sample StatsD config with `cp config/statsd.sample.json config/statsd.json`
- Optionally copy the sample syslog config with `cp config/syslog.sample.json config/syslog.json`
- Run `make` to install dependencies and run the test suite


Running Thundermole
-------------------

```
Usage: bin/thundermole [options]

Options:

  -h, --help                 output usage information
  -V, --version              output the version number
  -c, --config <path>        the directory to look for config files in
  -C, --cluster              create a cluster of workers for handling requests
  -p, --port <port>          the port to run on
  -w, --workers <num>        the number of workers to add to the cluster
  -r, --rewrite-host-header  whether to rewrite the host header
  -P, --ping-url             A ping URL for the application
```

### Configuration

`bin/thundermole` reads configurations from JSON files.

#### `routes.json`

Should contain application routes as outlined in the [routes option documentation](#routes-object). See [`routes.sample.json`](/config/routes.sample.json) for an example.

#### `statsd.json`

Optional. Should contain statsd configurations as outlined in the [statsd option documentation](#statsd-object). See [`statsd.sample.json`](/config/statsd.sample.json) for an example.

#### `syslog.json`

Should contain syslog configurations as outlined in the [winston-syslog documentation][winston-syslog]. See [`syslog.sample.json`](/config/syslog.sample.json) for an example.


JavaScript API
--------------

Thundermole can be loaded as a module and run programatically.


### Basic API

```js
var thundermole = require('thundermole');

// Create a thundermole application
var mole = thundermole({
    // ... options ...
});

// Start the thundermole application on port 3000
mole.listen(3000);
```


### Options

#### `routes` (object)

Required. A simple routing hash. Each property maps to the first part of a request path, and the value should indicate the API endpoint to use for matching requests. For example:

```json
{
    "foo": "http://my-api/"
}
```

Routes can also be regular expressions which gives you a little more flexibility. The URL path of the request is tested against, and has the preceeding slash stripped. It's worth noting that backslashes need to be escaped as we're working in JSON:

```json
{
    "/foo\\/bar/i": "http://my-api/"
}
```

Also regular expression routes do *not* match from the start and end of the path – you'll need to manually specify that:

```json
{
    "/^foo$/i": "http://my-api/"
}
```

#### `routes.default` (string)

Required. The default route to use if no others match the request.

#### `appendHeader` (string)

Optional. The header to send API-appended data in. Defaults to `X-Proxy-Appended-Data`.

#### `rewriteHostHeader` (boolean)

Optional. Whether to rewrite the `Host` header to match the proxy target. Defaults to `true`.

#### `statsd` (object)

Optional. A [node-statsd][node-statsd] configuration object, as outlined in their documentation.

#### `logger` (object)

Optional. An object with the methods `debug`, `error`, `info`, and `warn` which will be used to report errors and request information.

#### `pingUrl` (string)

Optional. A URL path (including preceding slash) which, when requested, will respond with a `200` status. This can be used for monitoring your Thundermole instance. Defaults to `null`.



Running Example Applications
----------------------------

Thundermole comes with an example application which runs two APIs and two backends. To run these you'll need to install [Foreman][foreman], or look into the example [Procfile](example/Procfile) and spin up each process separately.

If you have Foreman, run the example application with:

```
foreman start -d example
```


Building Thundermole-Compatible APIs
------------------------------------

In order for Thundermole to be able to communicate with your API, you'll need to follow the [API Specification](/docs/API-Specification.md).


Testing
-------

Lint the JavaScript and run the test suite:

```
make
```


License
-------

Copyright 2015 Nature Publishing Group.  
Thundermole is licensed under the [GNU General Public License 3.0][info-license].



[foreman]: https://github.com/ddollar/foreman
[node]: https://nodejs.org/
[node-statsd]: https://github.com/sivy/node-statsd
[winston-syslog]: https://github.com/winstonjs/winston-syslog

[info-license]: LICENSE
