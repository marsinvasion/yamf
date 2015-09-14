# YALV 

Yet Another Log Viewer

YALV helps you avoid the pain of manually tracing requests through logs. It requires an entering and exiting log entry based on which it calculates the time required for execution and matches against the server metrics like server memory used, memory free, load average.


## Getting Started

- Clone project

- Install dependencies

```
npm install
```

YALV has two main components on the server side. A udp server listens for logs being sent and stores it in mongodb. A bash script tails logs and sends it to the udp server.

Deploy the following on each server thats generating logs.

- Start the udp server on all servers. This listens for messages on port 5201

```
node server/udp.js
```

- Start the monitoring script
```
./monitor/tail.sh

or

./monitor/tail.sh /var/log/app.log pid 5201
```
In the first scenario it looks for app.log in the current working directory. It tails app.log and if it finds a string with pid in the log file it pushes to the udp server on port 5201.

The second option lets you give the location of the log file, custom string to look for in each log entry and the udp port.

- Mongodb

  The service uses mongodb for the backend.
  Mongodb location is configured in db/db.js. Change url variable to your location of mongodb instance
  If you would like to replace mongodb with another db, replace the functions in db/db.js with the equivalent for your db.

## Supported Platforms
- Linux/Mac
- Git bash on windows should work fine. (Load average will be [0,0,0] as that concept doesn't exist on windows)

## Generating logs
- Logs are generally generated by your application. However for testing purposes, you can generate dummy logs.
```
node jobs.js
```
This will generate logs in app.log in your current working directory. It uses bunyan and node cron jobs to generate logs.
Each log entry will look like 

```
{"name":"app","hostname":"L-SFM4142Q32","pid":8912,"level":30,"type":"entering","request":"2df4b3e3093da57548e0","api":"service","func":"calculate","msg":"","time":"2015-09-04T18:28:00.917Z","v":0}

...

{"name":"app","hostname":"L-SFM4142Q32","pid":8912,"level":30,"type":"exiting","request":"2df4b3e3093da57548e0","api":"service","func":"calculate","msg":"","time":"2015-09-04T18:28:01.379Z","v":0}
```

The service looks for type=entering,type=exiting and respective timestamps to match requests on same request,web server and func values.

If your  application doesn't generate json logs, you can modify either bash script or udp server to change your log string to json formatted string in the above format.

Looped calls to a function are all captured. Individual looped request are shown under all requests while the averaged out response times are shown in the other graphs.

## UI

To check out the graphical representation of your logs check out [YALV-UI](https://github.com/marsinvasion/yalv-ui)

## Dependencies

- [bunyan](https://github.com/trentm/node-bunyan): a JSON logging library for node.js services
- [node-cron-jobs](https://github.com/marsinvasion/node-cron-jobs): Schedules cron like jobs
- [express](https://github.com/git+https:/): Fast, unopinionated, minimalist we
b framework
- [express-handlebars](https://github.com/ericf/express-handlebars): A Handleba
rs view engine for Express which doesn&#39;t suck.
- [mongodb](https://github.com/mongodb/node-mongodb-native): A node.js driver f
or MongoDB


## License

MIT

Copyright (c) 2015 Anoop Kulkarni (http://anoopkulkarni.com)
