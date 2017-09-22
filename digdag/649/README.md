## Issue

* [FileNotFoundError occurs in python operator in docker](https://github.com/treasure-data/digdag/issues/649)

## Versions

### Host

- OS: Container Linux by CoreOS 1465.8.0
- Docker: 1.12.6

### digdag serer container

- OS: ubuntu 16.04
- Java: openjdk version 1.8.0_131
- digdag:  v0.9.16

### python container

- docker_image: python:3 (3.6.2-jessie)

## Reproduction

```
#Build docker image
~/issue-report/digdag/649 $ docker build -t digdag .

#Run digdag server
~/issue-report/digdag/649 $ docker run -d -p 65432:65432 -v /var/run/docker.sock:/var/run/docker.sock digdag

#Attach digdag client & push digdag project
~/issue-report/digdag/649 $ docker run -it --net host -v `pwd`/digdag-with-docker:/tmp/digdag-with-docker digdag /bin/bash
/# cd /tmp/
/tmp# digdag push python --project digdag-with-docker

#Start digdag server & show logs
/tmp# digdag start python digdag-with-docker --session now
/tmp# digdag log 1
```

notice: Digdag client also worked inside docker, but this is for simplicity and not essential.

## Logs

```
2017-09-22 17:58:07 +0000: Digdag v0.9.16
2017-09-22 17:57:13.562 +0000 [INFO] (0031@[0:python]+digdag-with-docker+print_python_version) io.digdag.core.agent.OperatorManager: sh>: python --version
Unable to find image 'python:3' locally
3: Pulling from library/python
aa18ad1a0d33: Pulling fs layer
15a33158a136: Pulling fs layer
f67323742a64: Pulling fs layer
c4b45e832c38: Pulling fs layer
b71152c33fd2: Pulling fs layer
d8eff2a5d585: Pulling fs layer
f80b3bd3dbda: Pulling fs layer
e5b3e7f65920: Pulling fs layer
c4b45e832c38: Waiting
b71152c33fd2: Waiting
d8eff2a5d585: Waiting
f80b3bd3dbda: Waiting
e5b3e7f65920: Waiting
15a33158a136: Verifying Checksum
15a33158a136: Download complete
f67323742a64: Verifying Checksum
f67323742a64: Download complete
b71152c33fd2: Verifying Checksum
b71152c33fd2: Download complete
d8eff2a5d585: Verifying Checksum
d8eff2a5d585: Download complete
c4b45e832c38: Verifying Checksum
c4b45e832c38: Download complete
f80b3bd3dbda: Verifying Checksum
f80b3bd3dbda: Download complete
e5b3e7f65920: Verifying Checksum
e5b3e7f65920: Download complete
aa18ad1a0d33: Verifying Checksum
aa18ad1a0d33: Download complete
aa18ad1a0d33: Pull complete
15a33158a136: Pull complete
f67323742a64: Pull complete
c4b45e832c38: Pull complete
b71152c33fd2: Pull complete
d8eff2a5d585: Pull complete
f80b3bd3dbda: Pull complete
e5b3e7f65920: Pull complete
Digest: sha256:2cbebfb9309a8b7b31c2fea8813436e297855f91cedd9cc28582678da905e5e0
Status: Downloaded newer image for python:3
Python 3.6.2
2017-09-22 17:57:56.051 +0000 [INFO] (0031@[0:python]+digdag-with-docker+hello) io.digdag.core.agent.OperatorManager: py>: scripts.myclass.MyClass.hello
Traceback (most recent call last):
  File "<stdin>", line 12, in <module>
FileNotFoundError: [Errno 2] No such file or directory: '.digdag/tmp/digdag-py-in-8498389327160892731.tmp'
2017-09-22 17:57:56.534 +0000 [ERROR] (0031@[0:python]+digdag-with-docker+hello) io.digdag.core.agent.OperatorManager: Task failed with unexpected error: Python command failed with code 1
java.lang.RuntimeException: Python command failed with code 1
	at io.digdag.standards.operator.PyOperatorFactory$PyOperator.runCode(PyOperatorFactory.java:153)
	at io.digdag.standards.operator.PyOperatorFactory$PyOperator.runTask(PyOperatorFactory.java:91)
	at io.digdag.util.BaseOperator.run(BaseOperator.java:35)
	at io.digdag.core.agent.OperatorManager.callExecutor(OperatorManager.java:312)
	at io.digdag.core.agent.OperatorManager.runWithWorkspace(OperatorManager.java:254)
	at io.digdag.core.agent.OperatorManager.lambda$runWithHeartbeat$2(OperatorManager.java:137)
	at io.digdag.core.agent.ExtractArchiveWorkspaceManager.withExtractedArchive(ExtractArchiveWorkspaceManager.java:36)
	at io.digdag.core.agent.OperatorManager.runWithHeartbeat(OperatorManager.java:135)
	at io.digdag.core.agent.OperatorManager.run(OperatorManager.java:119)
	at io.digdag.core.agent.MultiThreadAgent.lambda$null$0(MultiThreadAgent.java:127)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
2017-09-22 17:57:58.133 +0000 [INFO] (0031@[0:python]+digdag-with-docker^failure-alert) io.digdag.core.agent.OperatorManager: type: notify
```