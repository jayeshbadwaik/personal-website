---
title: "Docker in Docker"
authors: ["Jayesh Badwaik"]
layout: article
timestamp: true
toc: true
autonumbering: true
date : 2022-01-21T22:14:40+02:00
---

One of the jobs in any CI job is to build a docker container to run the
CI itself. One of the most secure ways to do so is to use the
[`docker-in-docker`](https://www.docker.com/blog/docker-can-now-run-within-docker/).
However, setting up docker-in-docker to run with different CI engines like
gitlab is not straightforward. In this blog post, I have tried to document the
process for future me with personal recommendations.

# Use a `cgroup2` Enabled Distribution

Using a `cgroup2` capable disitribution, so that you do not face problems with
more recent docker images.

```bash
→ grep cgroup /proc/filesystems
nodev   cgroup
nodev   cgroup2
```

# Enable Privileges for Docker in Gitlab Runner

```yaml
[[runners]]
  name = "your_gitlab_runner"
  [runners.docker]
    privileged = true
```

# Map Docker Sockets Properly

```yaml
[[runners]]
  name = "your_gitlab_runner"
  [runners.docker]
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
```

# Ensure Setting `TLS_CERTDIR` in `.gitlab-ci.yml`

```yaml
variables:
  DOCKER_TLS_CERTDIR: "/certs"
```


At this stage, you should be ready to run your docker in docker container.

