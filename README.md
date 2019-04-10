* * *
# Jenkins Build README
* * *

## Docker build

To ensure intermediate containers are removed during image build, use:

```
$ cd <Dockerfile dir>
$ docker build --force-rm -t amazonlinux:jenkins .

```


