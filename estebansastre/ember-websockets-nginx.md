# Ember Websockets & nginx integration


In [a previous article](http://estebansastre.com/ember-nginx-docker-deployment-with-multi-stage-builds/) I explained our approach at work to deploy an **Ember.js** application in an **nginx** server running on docker. Today I had to integrate an instance of that application to communicate with another microservice using **WebSockets**.

A simplified diagram of the architecture would be:

  ![diag_disp.png](diag_disp.png)


All this services run using a docker-compose script like this one (it is a very simplified version):

**docker-compose.yml**:
```yaml
  frontend:
    image: bde2020/ember-swarm-ui-frontend:0.6.0
    ports:
      - "88:80"
    links:
      - dispatcher:backend
    volumes:
      - ./config/frontend:/etc/nginx/conf.d

  dispatcher:
    image: semtech/mu-dispatcher:1.0.1
    links:
      - push-service:push-service
    volumes:
      - ./config/dispatcher:/config

  db:
    image: tenforce/virtuoso:1.2.0-virtuoso7.2.2

  push-service:
    image: tenforce/mu-push-service
    environment:
      - MU_SPARQL_ENDPOINT=http://database:8890/sparql
    links:
      - db:database
    ports:
      - "83:80"
```


The Dispatcher will proxy calls to other microservices based on the request path. This is very useful to avoid the frontend to have any information about other microservices host names. More information [here](https://github.com/mu-semtech/mu-dispatcher).

The push-service will listen for GET requests in the root path (/), open a websocket and start sending some json. The code in the frontend to test just taken from [ember-websockets](https://github.com/thoov/ember-websockets):



```javascript
export default Ember.Component.extend({
  websockets: Ember.inject.service(),

  didInsertElement() {
    this._super(...arguments);
    const socket = this.get('websockets').socketFor('ws://localhost/push-service/');
    ...
  }
```

But this is not enough, nginx needs additional configuration to open and maintain a connection using WebSockets. Luckily, nginx has support for it since version 1.3, and can be activated by specifying the set of headers that start the handshake for the websockets protocol.



```sh
# Set the server to proxy requests to when used in configuration
upstream backend_app {
    server backend;
}

# Server specifies the domain, and location the relative url
server {
    ...

    # WebSockets support
    location /push-service {
      proxy_pass http://backend_app;
      proxy_http_version 1.1;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $http_host;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
    }
  }
```

The call from the frontend is to *http://location/push-service*, but the Push-Service only understands calls to */*, then why specify the location = /push-service in nginx's configuration file? This is thanks to the *Dispatcher*, that will detect the call to *http://localhost/push-service* and rewrite it to *http://push-service/*, having a link to it in the docker-compose.yml file.


Have fun!

## References

  * [https://www.nginx.com/blog/websocket-nginx/](https://www.nginx.com/blog/websocket-nginx/)
  * [https://www.nginx.com/blog/realtime-applications-nginx/](https://www.nginx.com/blog/realtime-applications-nginx/)
  * [https://www.tutorialspoint.com/articles/how-to-configure-nginx-as-reverse-proxy-for-websocket](https://www.tutorialspoint.com/articles/how-to-configure-nginx-as-reverse-proxy-for-websocket)
  * [https://nginx.org/en/docs/http/websocket.html](https://nginx.org/en/docs/http/websocket.html)
  * [https://serverfault.com/questions/376162/how-can-i-create-a-location-in-nginx-that-works-with-and-without-a-trailing-slas](https://serverfault.com/questions/376162/how-can-i-create-a-location-in-nginx-that-works-with-and-without-a-trailing-slas)
