<img align="right" src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" width=150>

## Reference
1. [`docker run`](#docker-run)
2. [Basic work flow scripts: `docker cp`, `docker exec`, `docker kill`](#docker-workflows).
3. [`activate` a `python` virtual environment](#python).
4. [`unittest` a `python` project in a docker container](#python-unittest).
5. [Housekeeping](#housekeeping)

### 1. `docker run`
[Top](reference)

See images available for containers using `docker image ls` or search for a specific image using `docker images`.

Launch interactive bash in a container:

```
PS C:\Users\kjarv> docker images --filter=reference="datascience"
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
datascience         0.0                 0b724383f0d4        43 hours ago        3.15GB
PS C:\Users\kjarv> docker run -it --rm datascience:0.0 bash
user@61e4b0ecd539:~$ echo "Success!"
Success!
user@61e4b0ecd539:~$
```

Use `-v` to bind-mount a volume, exposing the files inside the running container, for a shortcut to mount the current directory to the container use command substitution `-v $(pwd):/container/path`:

```
PS C:\Users\kjarv> cd .\docker_tmp\
PS C:\Users\kjarv\docker_tmp> echo "Please!" > take_me_with_you.txt
PS C:\Users\kjarv\docker_tmp> docker run -it --rm `
>> -v C:\USers\kjarv\docker_tmp:/home/user/files `
>> datascience:0.0 bash

user@33b75c293967:~$ ls /home/user/files/
take_me_with_you.txt

```

### 2. Python
[Top](#reference)

Easily use an `activate`d `venv` (assume we have a `project` directory in `/home/user`, setup as a virtual environment):

```bash
#!/bin/bash

IMAGENAME=myimage

CONTAINER_ID=$(docker run -d -ti $IMAGENAME)

docker exec $CONTAINER_ID bash -c "echo 'source /home/user/project/bin/activate' >> /home/user/.bashrc"

docker exec -it $CONTAINER_ID bash

docker kill $CONTAINER_ID
```

### 3. Basic docker workflows
[Top](#reference)

I find the following to be a pretty useful workflow for working with content in a `localdir` host system directory:

```bash
#!/bin/bash

IMAGENAME=myimage

CONTAINER_ID=$(docker run -d -ti $IMAGENAME)

docker cp localdir/. $CONTAINER_ID:/home/user/localdir

docker exec -it $CONTAINER_ID bash

docker kill $CONTAINER_ID
```

### 4. Python unittest
[Top](#reference)

Suppose we have a `project` with `src` and `test` directories, containing `python` source code and test scripts, respectively. Assume `/home/user/project` already exists when the container is launched, because that directory was setup with `python -m venv` in the `Dockerfile`. An unambiguous way to run the tests is as follows:

```bash
#!/bin/bash

IMAGENAME=myimage

CONTAINER_ID=$(docker run -d -ti $IMAGENAME)

docker cp src/. $CONTAINER_ID:/home/user/project/src
docker cp test/. $CONTAINER_ID:/home/user/project/test

docker exec -it $CONTAINER_ID bash -c "source /home/user/project/bin/activate && \
cd /home/user/project/ && \ 
python -m unittest discover -s test"

docker kill $CONTAINER_ID
```

### 5. Housekeeping
[Top](#reference)

See containers with `docker container ls`. To tidy unused images run `docker image prune`, aggressively tidy by launching containers using images you want to keep, then `docker image prune -a`.

Sometimes we can have dangling images, that show up like this:

```
PS C:\Users\kjarv> docker images -f "dangling=true"
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
<none>              <none>              63291e9a689c        7 weeks ago         63.3MB
```

Output the `IMAGE ID` using the `-q` flag and pass this to `docker rmi` to remove the dangling image. If the image has been used by a container, force the images removal using `-f`, or prune stopped containers:

View containers with `docker container ls -a`.

Prune containers with `docker container prune`.

Cleanup hanging images after pruning stopped containers:

`docker rmi $(docker images -f "dangling=true" -q)`



