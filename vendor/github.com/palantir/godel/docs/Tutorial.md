The following tutorial demonstrates how to install, configure and use gödel on an example project. We will be creating a
project called echgo2 that is a simple program that echoes user input in a variety of ways.

Following every step of the tutorial from beginning to end will show the entire end-to-end process of creating a new
project and using a variety of gödel features to configure it. It is also possible to jump to any section of interest
directly. Each step of the tutorial provides a general summary of the step, the expected preconditions before the step,
the actions to take during the step, and the conditions that should exist after the step.

The repository at https://github.com/nmiyake/echgo2 contains the result of walking through the tutorial.

The tutorial consists of the following steps:

* [Add gödel to a project](https://github.com/palantir/godel/wiki/Add-g%C3%B6del)
* [Add Git hooks to enforce formatting](https://github.com/palantir/godel/wiki/Add-git-hooks)
* [Generate IDE project for Gogland](https://github.com/palantir/godel/wiki/Generate-IDE-project)
* [Format Go files](https://github.com/palantir/godel/wiki/Format)
* [Run static checks on code](https://github.com/palantir/godel/wiki/Check)
* [Run tests](https://github.com/palantir/godel/wiki/Test)
* [Build](https://github.com/palantir/godel/wiki/Build)
* [Run](https://github.com/palantir/godel/wiki/Run)
* [Dist](https://github.com/palantir/godel/wiki/Dist)
* [Publish](https://github.com/palantir/godel/wiki/Publish)
* [Generate license headers](https://github.com/palantir/godel/wiki/License-headers)
* [Go generate tasks](https://github.com/palantir/godel/wiki/Generate)
* [Define excludes](https://github.com/palantir/godel/wiki/Exclude)
* [Write integration tests](https://github.com/palantir/godel/wiki/Integration-tests)
* [Sync a documentation directory with GitHub wiki](https://github.com/palantir/godel/wiki/GitHub-wiki)
* [Verify project](https://github.com/palantir/godel/wiki/Verify)
* [Set up CI to run tasks](https://github.com/palantir/godel/wiki/CI-setup)
* [Update gödel](https://github.com/palantir/godel/wiki/Update-g%C3%B6del)
* [Other commands](https://github.com/palantir/godel/wiki/Other-commands)
* [Conclusion](https://github.com/palantir/godel/wiki/Tutorial-conclusion)

It is recommended that the tutorial be run in a Docker image to provide maximal environment isolation. A base Docker
image that can be used for the tutorial is available in the `docs/templates/baseimage` directory.

The Dockerfile in the directory has some values specified using `ENV` that can be customized if desired. If you want to
run the tutorial directly on a host rather than in a Docker image, you should set/export the `ENV` values.

This tutorial uses `github.com/nmiyake/echgo2` as the project path. This is fine for the main path of the tutorial.
However, if you want to walk through publishing the repository to GitHub, you should modify this project path to be a
path to a repository that you can create/push to GitHub (although it is possible to push a project with this path to any
GitHub repository, using a project path that mirrors the GitHub location is more realistic).

Start the tutorial by building the Docker image:

```
➜ cd ${GOPATH}/src/github.com/palantir/godel/docs/templates/baseimage
➜ ./build.sh
➜  baseimage git:(updatetutorial-usedocker-submit) ✗ ./build.sh
Sending build context to Docker daemon  3.584kB
Step 1/9 : FROM godeltutorial:setup
 ---> cf02231516b7
Step 2/9 : ENV GODEL_VERSION 2.0.0-rc5
 ---> Running in 5edef2973ec8
Removing intermediate container 5edef2973ec8
 ---> a31a93d9c1b5
Step 3/9 : ENV GODEL_CHECKSUM 08d9ed3e33e69006a9c58ec65cef0ad9bd17af4c73b5c1d1aa116e813a954314
 ---> Running in 204d4784866d
Removing intermediate container 204d4784866d
 ---> 1fb055da5c26
Step 4/9 : ENV PROJECT_PATH github.com/nmiyake/echgo2
 ---> Running in 8ea1a7b8e296
Removing intermediate container 8ea1a7b8e296
 ---> 9e896868efd2
Step 5/9 : ENV GIT_USERNAME "Tutorial User"
 ---> Running in 8196119c82ed
Removing intermediate container 8196119c82ed
 ---> 799c306ce276
Step 6/9 : ENV GIT_EMAIL "tutorial@tutorial-user.com"
 ---> Running in 22b80563fdab
Removing intermediate container 22b80563fdab
 ---> cdfc69253319
Step 7/9 : RUN apt-get update && apt-get install -y tree
 ---> Running in 12f566bc04a1
Ign:1 http://deb.debian.org/debian stretch InRelease
Hit:2 http://security.debian.org stretch/updates InRelease
Hit:3 http://deb.debian.org/debian stretch-updates InRelease
Hit:4 http://deb.debian.org/debian stretch Release
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
tree is already the newest version (1.7.0-5).
0 upgraded, 0 newly installed, 0 to remove and 5 not upgraded.
Removing intermediate container 12f566bc04a1
 ---> b5cb8a79a6cc
Step 8/9 : RUN git config --global user.name "${GIT_USERNAME}" &&     git config --global user.email "${GIT_EMAIL}" &&     mkdir -p ${GOPATH}/src/${PROJECT_PATH}
 ---> Running in 73a28cc0f3e3
Removing intermediate container 73a28cc0f3e3
 ---> 6b746e11b8d0
Step 9/9 : WORKDIR /go/src/${PROJECT_PATH}
Removing intermediate container 1d583c4c8c77
 ---> 8b99407e39f9
Successfully built 8b99407e39f9
Successfully tagged godeltutorial:setup
```

Now, run the Docker image interactively:

```
➜ docker run -it godeltutorial:setup
root@010fdbb9adec:/go/src/github.com/nmiyake/echgo2#
```

You can now follow the rest of the tutorial. The following steps are also valid on a host as long as the proper
environment variables are set and the required programs are available.

Start the tutorial by creating the directory for your project and setting it to be the working directory:

```
➜ mkdir -p ${GOPATH}/src/${PROJECT_PATH} && cd $_
➜ pwd
/go/src/github.com/nmiyake/echgo2
```

Initialize a git repository, add a README and commit it:

```
➜ git init
Initialized empty Git repository in /go/src/github.com/nmiyake/echgo2/.git/
➜ echo 'echgo2 is a program that echoes input provided by the user.' > README.md
➜ git add README.md
➜ git commit -m "Initial commit"
[master (root-commit) d58bb82] Initial commit
 1 file changed, 1 insertion(+)
 create mode 100644 README.md
```

Tutorial end state
------------------
* `${GOPATH}/src/${PROJECT_PATH}` exists, is the working directory and is initialized as a Git repository

Tutorial next step
------------------
[Add gödel](https://github.com/palantir/godel/wiki/Add-godel)
