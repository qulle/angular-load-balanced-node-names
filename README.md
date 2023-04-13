# Angular Load Balanced Node Names

## About

Getting the node name from the responding server that is behind a load balancer.

## Two Servers

It is a common way to use a alias that points to some sort of load balancing device that can either send all traffic to one primary server and use the second one as stand by if to much traffic is sent.  Or it can alternate, sending traffic to either one of the servers. But how do you know what server that responded to the request? 
```
                                                         /--> WEBSERVER001
$ http --> web-alias.example.local --> Load Balancer --> |
                                                         \--> WEBSERVER002
```

The solution is to enable a custom header on each webserver and allowing it to be read by the browser using JS.

## Custom Header

Custom static headers can be added in the IIS Manager under the `HTTP Response Headers` section. 
```
ServerNodeName: 'WEBSERVER001'
```

Normally it is only CORS headers that can be read by JS in the browser. All other headers can only be displayed in the browser itselfe under the developer tools. In order for us to read our custom header, a second header must be added.
```
Access-Control-Expose-Headers: 'ServerNodeName'
```

## Fetching Full Response

Normally a API-request using the HttpClient will get back the body of a response. In order to view the headers as well the full response needs to be monitored.
```typescript
class ApiService {
    constructor(
        private readonly http: HttpClient
    ) {
        this.url = environment.api.url;
    }
    
    private getHeader(headers: HttpHeaders, name: string): string {
        return headers.get(name) || `Missing header [${name}]`;
    }

    getUser(username: string, locale: string): Observable<RequestWrapper<User>> {
        const url: string = `${this.url}/v1/user/${username}?locale=${locale}`;
        const startTimestamp: moment.Moment = moment();

        return this.http
            .get<RequestWrapper<User>>(url, {
                headers: {
                    Accept: 'application/json'
                },
                observe: 'response',
            })
            .pipe(
                map(response => {
                    if (!response.body) {
                        return <RequestWrapper<User>>{};
                    }

                    // Add custom response parameters
                    response.body.serverName = this.getHeader(response.headers, 'ServerNodeName');
                    response.body.url = url;

                    return response.body;
                })
            );
    }
}
```

The example above uses the following data models.
```typescript
export interface RequestWrapper<T> {
    serverName?: string;
    url?: string;
    message: T;
    result: boolean;
    errorMessage: string;
}
```

## Author
[Qulle](https://github.com/qulle/)
