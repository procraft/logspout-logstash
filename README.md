# logspout-logstash

[![Docker Hub](https://img.shields.io/docker/pulls/procraft/logspout-logstash.svg?maxAge=2592000?style=plastic)](https://hub.docker.com/r/procraft/logspout-logstash/)
[![](https://img.shields.io/docker/automated/procraft/logspout-logstash.svg?maxAge=2592000)](https://hub.docker.com/r/procraft/logspout-logstash/builds/) [![](https://images.microbadger.com/badges/image/procraft/logspout-logstash.svg)](https://microbadger.com/images/procraft/logspout-logstash "Get your own image badge on microbadger.com")


Tiny [Logspout](https://github.com/gliderlabs/logspout) adapter to send Docker container logs to [Logstash](https://github.com/elastic/logstash) via UDP or TCP. This just the hosted working version of [looplab/logspout-logstash](https://github.com/looplab/logspout-logstash).


## Example

A sample `docker-compose.yaml` file:

```yaml
version: "2"
services:
  logspout:
    image: procraft/logspout-logstash
    restart: on-failure
    environment:
      ROUTE_URIS: logstash://logstash:5000
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock

  logstash:
    image: logstash:2.3
    command: -f /opt/logstash/sample.conf
    volumes:
      - ./logstash/:/opt/logstash


    # This is just an example.
    # Normally you would put your own services in this file.
    # Similar setup works on Kubernetes as well.
    redis:
      image: redis
      restart: always
```


A sample Logstash configuration `logstash/sample.conf`:

```conf
input {
  udp {
    port  => 5000
    codec => json
  }
}


filter {
  if [docker][image] =~ /^logstash/ {
    drop { }
  }
}


output {
  elasticsearch {
    hosts => ["localhost:9200"]
  }
  stdout { codec => rubydebug }
}
```
