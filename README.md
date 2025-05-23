## Kokkos with OpenMP parallel host backend in VS Code with a Dev Container

We will see how to develop in VS Code using a Docker container based development environment that involves C++ development using the Kokkos library with OpenMP as the parallel programming backend on the CPU.

#### Steps to follow
* Installation of Visual Studio Code. 
* Installation of necessary extensions in VS Code. See below for details.
* Installation of a container management tool like Docker or Podman. 
* Opening the directory `devc_hellow` in VS Code.
* VS Code may automatically ask you to reopen the project in a container. Otherwise, you could manually do so by opening the Command Pallette (Ctrl+Shift+P) and entering ">Dev Containers: Reopen in Container".
* If all goes well, all the requirements would be installed including a C++ compiler, CMake and Kokkos. A "build" directory would also be generated with the C++ project, ready to be built.

#### The development environment
* Ubuntu 22.04 is the base image of the container.
* The C++ compiler is GCC, installed via `build-essential`.
* Kokkos version is 4.6.01, with OpenMP parallel host backend enabled. 

#### Here are further notes to get a working system

##### Installation of Docker in the host
[How to install and use docker on Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)

Give user [permission](https://stackoverflow.com/questions/48957195/how-to-fix-docker-permission-denied) to run Docker.

Ensure that you haven’t installed different parts of Docker in different ways (say, some via snap and some via apt) or have multiple installations of Docker (one via snap and one via apt). This will lead to error while trying to rebuild the container from inside VS Code. A [few](https://forums.docker.com/t/can-not-stop-docker-container-permission-denied-error/41142) [references](https://stackoverflow.com/questions/47223280/docker-containers-can-not-be-stopped-or-removed-permission-denied-error) [about](https://stackoverflow.com/questions/71477749/error-response-from-daemon-cannot-kill-container-permission-denied-how-to-kil) this point. 

##### Installation of extensions in host VS Code
* “Dev Containers” extension. Note that the “Remote Development” extension contains “Dev Containers” too.
* “Docker” extension. Currently this extension will be installed in the Dev Container environment also. Note that the "Docker" extension is going to become "Container Tools" extension.

Note: “C/C++ Extension Pack” will be installed in the Dev Container environment. No harm if they are both in the host and the remote.

#### Observations

* In `devcontainer.json`, one can specify a “postStartCommand”. For e.g., it is defined as “cmake -DKokkos_ROOT=/opt/kokkos-install -DKokkos_ENABLE_OPENMP=ON -B build" in `hellow_devc/.devcontainer/devcontainer.json`. Currently, this option works only with Docker and not with Podman.
One gets the following error with Podman:
Error: error parsing environment variables: name "\afbfa9712-93dc-4bca-8ecc-99ffb315ae27\x1b]0;vscode@mdlspc111.extra.cea.fr: cat /proc/self/environ\aREMOTE_CONTAINERS_IPC" has white spaces, poorly formatted name
[147463 ms] postStartCommand from devcontainer.json failed with exit code 125. Skipping any further user-provided commands.
* Podman takes more time to build the container. Probably, it takes more time to restart a container also.
* With Podman, VS Code disconnects and reconnects to the container from time to time.

#### Some technical points
* In order to enable networking while building the container, define `"build"` as `"build": { …, "options": ["--network=host"], …}`. This may be needed in order to `git clone` the Kokkos repository.  Also, note that `“runArgs”: [“--network=host”]` is for enabling network connection while running the container.  
