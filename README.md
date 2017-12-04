# Simple rsyslog server

## Preconfig

```
mkdir logs
chmod -R 777 logs
```

## Build

```
docker build -t croc/rsyslog .
```

## Run

```
docker-compose up -d
```

## Usage

This container's rsyslog service listen on 5000 port (TCP and UDP too).

You can send the client's log to these ports.

### Linux logs

How to send Linux's logs to a remote log server? Easily :)
If you use rsyslog (default on newer distributions) create a file (example `90-remote.conf` ) in the rsyslog's config folder:

`/etc/rsyslog.d/90-remote.conf`
```
*.*   @192.168.199.87:5000
```

where is:
  - `*.*` - the rsyslog will send all logs to the remote host
  - `192.168.199.87` - the IP of the remote host
  - `5000` - the listen port of the syslog server

... and restart the service
```
systemctl restart rsyslog
```
or the server with the `reboot` command

That's it. The server's rsyslog sends all log to the remote server too.

### Docker logs

The easiest solution if you use GlinderLabs's Logspout container.
  - https://github.com/gliderlabs/logspout

Example:
```
docker run -d --name logspout --restart always -v /var/run/docker.sock:/var/run/docker.sock gliderlabs/logspout syslog://192.168.199.87:5000
```

where is:
  - `syslog://192.168.199.87:5000` - the URL and protocol of the remote syslog server
  - other parameter is default or recommended for the logspout container

The logspout container sends all docker logs to the remote server, and you can check the log of the docker containers locally too.

### Test

You can test the works of the syslog server.

Method 1.:
```
logger --server 192.168.199.87 --port 5000 my test message 1
```

Method 2.:
```
echo "my test message 1" | nc 192.168.199.87 5000
```


Good Luck!
 
