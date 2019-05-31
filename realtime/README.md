
<https://css-tricks.com/the-many-ways-of-getting-data-into-charts/>


## Web Socket

Websockets and SSE (Server Sent Events) are both capable of pushing data to browsers, however they are not competing technologies. 

Websockets connections can both send data to the browser and receive data from the browser. A good example of an application that could use websockets is a chat application.

<https://blog.easyaspy.org/post/10/2019-04-30-creating-real-time-charts-with-flask>

<https://redstapler.co/javascript-realtime-chart-plotly/>

<https://stackoverflow.com/questions/5195452/websockets-vs-server-sent-events-eventsource>

<https://github.com/duyetdev/realtime-dashboard>

<https://developer.ibm.com/tutorials/realtime-visitor-analysis-with-kafka/>

## SSE

<https://en.wikipedia.org/wiki/Server-sent_events>

<https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events>

text/event-stream is the official media type for Server Sent Events (SSE); it will prefix data bits with a data: prefix and you can also choose your prefix to change the meaning of that piece of data for the client. This media type is for browsers, as they support that using the EventSource JavaScript API.

application/stream+json is for server to server/http client (anything that's not a browser) communications. It won't prefix the data and will just use CRLF to split the pieces of data. 
<https://hpbn.co/server-sent-events-sse/>

<https://www.w3schools.com/html/html5_serversentevents.asp>

Server-sent events (SSE) is a technology where a browser receives automatic updates from a server via HTTP connection
(standardized in HTML5 standards). 

Kafka doesn’t natively support this protocol, so we need to add an additional service to make this happen.

<https://hackernoon.com/supercharging-kafka-enable-realtime-web-streaming-by-adding-pushpin-fd62a9809d94>

## Kafka WebSocket
<https://www.confluent.io/blog/consuming-messages-out-of-apache-kafka-in-a-browser>

## Mosquitto  
$ mosquitto -c /path/to/mosquitto.conf
port 1883

listener 1884
protocol websockets

client = new Paho.MQTT.Client("localhost", 1883, "myclientid_" + parseInt(Math.random() * 100, 10));

## MQTT Websocket  

https://www.eclipse.org/paho/clients/js/

https://www.cloudmqtt.com/docs-websocket.html

<https://www.cumulocity.com/guides/mqtt/hello-mqtt-javascript/>

https://gist.github.com/matbor/9825309

http://www.astracorp.com/blog/mqtt-over-websockets

https://stackoverflow.com/questions/30624897/direct-mqtt-vs-mqtt-over-websocket

http://www.steves-internet-guide.com/mqtt-websockets/

http://www.steves-internet-guide.com/using-javascript-mqtt-client-websockets/

http://www.steves-internet-guide.com/wp-content/uploads/javascript-websockets.7z

https://github.com/mqttjs/MQTT.js

https://jpmens.net/2014/07/03/the-mosquitto-mqtt-broker-gets-websockets-support/

## MQTT plotting

https://tinker.yeoman.com.au/2015/05/11/simple-browser-based-graphical-display-of-mqtt-data/

<http://www.mqtt-dashboard.com/metrics.html>

## HighCharts

<https://www.npmjs.com/package/highcharts>

https://tech.scargill.net/node-red-and-highcharts/

https://medium.com/@chakrar27/mqtt-highcharts-a-powerful-combo-for-building-real-time-dashboards-2d97880ee01d

https://github.com/matbor/mqtt2highcharts

https://gist.github.com/vitormeriat/b6de7aa0a28af2b45f15c3d67721e051


## JS plotting libraries

<https://www.codewall.co.uk/the-best-javascript-data-visualization-charting-libraries/>

<https://blog.bitsrc.io/11-javascript-charts-and-data-visualization-libraries-for-2018-f01a283a5727>

<https://habr.com/company/ruvds/blog/423983/>

## React plotting

<https://cube.dev/blog/react-visualization-libraries-in-2019/>

## Real-time plotting

<https://itnext.io/javascript-real-time-visualization-of-high-frequency-streams-d6533c774794>

<https://pusher.com/tutorials/live-graph-d3>

<https://github.com/hasura/graphql-engine/tree/master/community/tools/graphql2chartjs>

<https://itnext.io/javascript-real-time-visualization-of-high-frequency-streams-d6533c774794>

<https://medium.com/@xaviergeerinck/building-a-real-time-streaming-dashboard-with-spark-grafana-chronograf-and-influxdb-e262b68087de>
