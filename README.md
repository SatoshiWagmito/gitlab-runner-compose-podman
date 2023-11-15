# GitLab Runner Compose with Podman Example

I was in need of a quick example of setting up a local GitLab instance,
running on Podman.
So here is a quick and dirty docker-compose setup for doing that.

At the end of this brief example you might also want to have a peek at
<https://docs.gitlab.com/runner/executors/docker.html#use-podman-to-run-docker-commands>

IMHO, Let's just say that while Podman is the future:) it still has some ecosystem quirks bugs and issues, some in Podman, or in friends. But, getting there, every day.

## Requirements (only tested on Ubuntu 22.04)

- Podman 4.6.2++ installed and working. This means

    Podman running as a service:

    > podman info | grep -A2 'remoteSocket' # should list exists true and the socket

    Alias has been set, bye docker

    >  alias docker=podman # has been set

    DOCKER_HOST has been exported (sneaky, here is the podman socket)

    >export DOCKER_HOST=unix:///run/user/1000/podman/podman.sock or whatever your podman socket is

- docker-compose /yes you sadly still need this as podman-compose is not up to the task yet. But we are using podman underneath.

## Running steps

1. docker-compose up

    > Change root password for gitlabce

2. podman exec -it gitlab gitlab-rake "gitlab:password:reset[root]"

3. docker-compose down && docker-compose un

4. Go to the GitLab UI at 127.0.0.1 or 10.89.0.2 :) and login with root/pw-you-changed-to

5. Search for admin area in GUI,

6. Add new instance runner under CI (if the ip changes suddenly here after applying, just change it back and enter)

7. You should now see an instruction page for how to register the runner, and a token.

8. Follow the above steps by exec into the running runner and do i.e run gitlab-runner register. (you will change some values later so dont worry if there are some errors here).

    > podman exec -it gitlab-runner /bin/bash
    > gitlab-runner register
    > ...follow steps

9. Still in the runner container do: vi /etc/gitlab-runner/config.toml

10. Have a look at the runner.example.toml in this project and add what you need: host, privileged and FF_NET.. are needed for podman at least.

11. docker-compose down && docker-compose, login to ui, check that the runner is online in the admin area

12. Add a testproject, ci pipes, have fun!!
