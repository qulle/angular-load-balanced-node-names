# Angular Load Balanced Node Names

## About

Getting the node name from the responding server that is behind a load balancer.

## Two Servers

It is a common way to use a alias that points to some sort of load balancing device that can either send all traffic to one primary server and use the second one as stand by if to much traffic is sent.  Or it can alternate, sending traffic to either one of the servers. But how do you know what server that responded to the request? 

The solution is to enable a custom header on each webserver and allowing it to be read by the browser using JS.

## Custom Header

Custom static headers can be added in the IIS Manager under the `HTTP Response Headers` section. 
```
ServerName: WEBSERVER001
```

Normally it is only CORS headers that can be read by JS in the browser. All other headers can only be displayed in the browser itselfe under the developer tools. In order for us to read our custom header, a second header must be added.
```

```

## Fetching Full Response

Normally a API-request using the HttpClient will get back the body of a response. In order to view the headers as well the full response needs to be monitored.

## Author
[Qulle](https://github.com/qulle/)