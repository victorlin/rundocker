Rundocker
=========

A script for running docker container process

The issue we are solving here
=============================

Since docker 0.6, you can actually run docker container like normal process

```
docker run --rm foobar
```

when you press Ctrl + C, docker client will proxy the INT signal for you to the docker container, as a result, container will be terminated. However, in many cases, docker container will not be closed correctly, even the docker client process is stopped. For example, when you tried to run a container with an used port, the run command will fail, however, somehow the docker container will not be removed, and it causes following run commands fail

```
2014/07/27 03:24:45 Error response from daemon: Cannot start container 08b25a41e30be78410a2556ffe01e720ff0d7bd512a53e8a44d7bceb8d3cf83e: Bind for 172.17.42.1:5050 failed: port is already allocated
2014/07/27 03:24:46 Error response from daemon: Conflict, The name foobar is already assigned to 08b25a41e30b. You have to delete (or rename) that container to be able to assign foobar to a container again.
2014/07/27 03:24:48 Error response from daemon: Conflict, The name foobar is already assigned to 08b25a41e30b. You have to delete (or rename) that container to be able to assign foobar to a container again.
....
```

There is an [issue #7245](https://github.com/docker/docker/issues/7245) describes the problem. If new feature is added, I think we can drop the project then.

Usage
=====

Use rundocker to run docker with a name as you usually do

```
sudo rundocker --rm --name="foobar" eggs/spam
```

How it works
============

The script looks into `--cidfile` argument, if it is provided, before docker run is executed, it will ensure the container is removed. If `--cidfile` is not provided, it will use `/var/run/<name>.cid`, and pass it to docker run for you.

When the script receives INT signal, it will stop the container.
