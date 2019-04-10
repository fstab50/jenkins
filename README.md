* * *
# Jenkins Build README
* * *

## Docker build

To ensure intermediate containers are removed during image build, use:

```
$ cd <Dockerfile dir>
$ docker build --force-rm -t amazonlinux:jenkins .

```

##  Docker run (create container)

```
docker run --name jenkinstest -p 8080:8080 -p 50000:50000 -it amazonlinux:jenkins tail -f /dev/null
```

* * *
