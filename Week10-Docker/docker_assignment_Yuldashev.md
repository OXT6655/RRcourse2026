# Docker Assignment
**Reproducible Research · University of Warsaw**
**Otajon Yuldashev**

---

## Task 1.1 — Change the Python version

```
PS C:\Users\yulda\hello-docker> @'
>> FROM python:3.9-slim
>> WORKDIR /app
>> COPY hello.py .
>> CMD ["python", "hello.py"]
>> '@ | Out-File -FilePath Dockerfile -Encoding utf8

PS C:\Users\yulda\hello-docker> docker build -t hello-docker .
[+] Building 17.2s (8/8) FINISHED                                                                  docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                               0.0s
 => => transferring dockerfile: 118B                                                                               0.0s
 => [internal] load metadata for docker.io/library/python:3.9-slim                                                 2.5s
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 2B                                                                                    0.0s
 => [1/3] FROM docker.io/library/python:3.9-slim@sha256:2d97f6910b16bd338d3060f261f53f144965f755599aab1acda1e13c  14.0s
 => => resolve docker.io/library/python:3.9-slim@sha256:2d97f6910b16bd338d3060f261f53f144965f755599aab1acda1e13cf  0.0s
 => => sha256:ea56f685404adf81680322f152d2cfec62115b30dda481c2c450078315beb508 251B / 251B                         0.4s
 => => sha256:fc74430849022d13b0d44b8969a953f842f59c6e9d1a0c2c83d710affa286c08 13.88MB / 13.88MB                   9.8s
 => => sha256:b3ec39b36ae8c03a3e09854de4ec4aa08381dfed84a9daa075048c2e3df3881d 1.29MB / 1.29MB                     2.5s
 => => sha256:38513bd7256313495cdd83b3b0915a633cfa475dc2a07072ab2c8d191020ca5d 29.78MB / 29.78MB                  12.6s
 => => extracting sha256:38513bd7256313495cdd83b3b0915a633cfa475dc2a07072ab2c8d191020ca5d                          0.8s
 => => extracting sha256:b3ec39b36ae8c03a3e09854de4ec4aa08381dfed84a9daa075048c2e3df3881d                          0.1s
 => => extracting sha256:fc74430849022d13b0d44b8969a953f842f59c6e9d1a0c2c83d710affa286c08                          0.4s
 => => extracting sha256:ea56f685404adf81680322f152d2cfec62115b30dda481c2c450078315beb508                          0.0s
 => [internal] load build context                                                                                  0.0s
 => => transferring context: 29B                                                                                   0.0s
 => [2/3] WORKDIR /app                                                                                             0.2s
 => [3/3] COPY hello.py .                                                                                          0.0s
 => exporting to image                                                                                             0.3s
 => => exporting layers                                                                                            0.1s
 => => exporting manifest sha256:c690254bbfd17b4573dd5e3e2267f271a4d8f43d84fd5072a42990f775e37c91                  0.0s
 => => exporting config sha256:cecf2131b1fcc8b4b3632c085a11428f3099c1e403713366b095807e5b03528f                    0.0s
 => => exporting attestation manifest sha256:7b6ab8f29538c2fd60b3bbe16bb028c6fceea882a21c5ef87fc0804dbc7c58ba      0.0s
 => => exporting manifest list sha256:58340d285b5152b9bf5f4700a75cee55c8db8a5ca59ef6b3d8f7047f81ecedc7             0.0s
 => => naming to docker.io/library/hello-docker:latest                                                             0.0s
 => => unpacking to docker.io/library/hello-docker:latest                                                          0.1s

PS C:\Users\yulda\hello-docker> docker run --rm hello-docker
Hello from Python 3.9 inside a container!
```

---

## Task 1.2 — Break and fix the Dockerfile

### Failed run (pandas not installed in image)

```
PS C:\Users\yulda\hello-docker> @'
>> import sys, pandas
>> print(f"Python {sys.version_info.major}.{sys.version_info.minor}, pandas {pandas.__version__}")
>> '@ | Out-File -FilePath hello.py -Encoding utf8

PS C:\Users\yulda\hello-docker> docker build -t hello-docker .
[+] Building 1.2s (8/8) FINISHED                                                                   docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                               0.0s
 => => transferring dockerfile: 118B                                                                               0.0s
 => [internal] load metadata for docker.io/library/python:3.9-slim                                                 0.7s
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 2B                                                                                    0.0s
 => [1/3] FROM docker.io/library/python:3.9-slim@sha256:2d97f6910b16bd338d3060f261f53f144965f755599aab1acda1e13cf  0.0s
 => CACHED [2/3] WORKDIR /app                                                                                      0.0s
 => [3/3] COPY hello.py .                                                                                          0.0s
 => exporting to image                                                                                             0.3s
 => naming to docker.io/library/hello-docker:latest                                                                0.0s

PS C:\Users\yulda\hello-docker> docker run --rm hello-docker
Traceback (most recent call last):
  File "/app/hello.py", line 1, in <module>
    import sys, pandas
ModuleNotFoundError: No module named 'pandas'
```

### Fix: added `RUN pip install pandas==2.2.2` to the Dockerfile

```
PS C:\Users\yulda\hello-docker> @'
>> FROM python:3.9-slim
>> WORKDIR /app
>> RUN pip install pandas==2.2.2
>> COPY hello.py .
>> CMD ["python", "hello.py"]
>> '@ | Out-File -FilePath Dockerfile -Encoding utf8

PS C:\Users\yulda\hello-docker> docker build -t hello-docker .
[+] Building 62.5s (10/10) FINISHED                                                                docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                               0.0s
 => => transferring dockerfile: 148B                                                                               0.0s
 => [internal] load metadata for docker.io/library/python:3.9-slim                                                 1.6s
 => [1/4] FROM docker.io/library/python:3.9-slim@sha256:2d97f6910b16bd338d3060f261f53f144965f755599aab1acda1e13cf  0.0s
 => CACHED [2/4] WORKDIR /app                                                                                      0.0s
 => [3/4] RUN pip install pandas==2.2.2                                                                           52.0s
 => [4/4] COPY hello.py .                                                                                          0.1s
 => exporting to image                                                                                             8.7s
 => naming to docker.io/library/hello-docker:latest                                                                0.0s
 => unpacking to docker.io/library/hello-docker:latest                                                             1.6s

PS C:\Users\yulda\hello-docker> docker run --rm hello-docker
Python 3.9, pandas 2.2.2
```

### Final Dockerfile

```dockerfile
FROM python:3.9-slim
WORKDIR /app
RUN pip install pandas==2.2.2
COPY hello.py .
CMD ["python", "hello.py"]
```

---

## Question 2.1 — Why pin?

Writing `RUN pip install pandas` without a version number means Docker will always fetch the latest available release at build time. This is a reproducibility problem because the latest version of pandas changes over time — a colleague building the same Dockerfile six months later could get pandas 3.x instead of 2.x, which may have breaking API changes. For example, certain functions deprecated in pandas 2.0 were removed in later versions, so code that ran fine today could raise errors tomorrow with no changes to the source files. The whole point of a Dockerfile is to freeze the environment, and an unpinned dependency is a hidden moving part that silently breaks that guarantee.

## Question 2.2 — Recipe or cake?

Sending the Dockerfile and `hello.py` (the recipe) is better for reproducible research than sending the built image (the cake). A built image is a binary blob: a reviewer cannot inspect what is inside it, verify that it matches the source code, or audit the exact steps used to produce it. The Dockerfile, by contrast, is plain text — it is human-readable, version-controllable, and diff-able. This means that when the environment needs to change (e.g., a security patch to the base image), a collaborator can update one line and rebuild, rather than receiving an entirely new opaque binary. Transparency of process, not just reproducibility of output, is what makes research verifiable.
