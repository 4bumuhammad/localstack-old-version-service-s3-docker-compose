# Localstack 2.0.0


**Definisi localstack.** <br />
LocalStack adalah sebuah alat pengembangan perangkat lunak open-source yang menyediakan lingkungan simulasi awan lokal untuk pengembangan dan pengujian aplikasi cloud. Ini memungkinkan pengembang untuk membuat replika lingkungan cloud di mesin lokal mereka, memungkinkan pengembangan dan pengujian aplikasi tanpa perlu terhubung ke layanan cloud yang sebenarnya.

&nbsp;

<div align="center">
    <img src="./gambar-petunjuk/ss_localstack_2.0.0_dockerhub.png" alt="ss_localstack_2.0.0_dockerhub" style="display: block; margin: 0 auto;">
    <p align="center">URL : https://hub.docker.com/layers/localstack/localstack/2.0.0/images/sha256-2d0861a7fd281bb4f8a8404d8249ab4aed278c5ac8bdc55f8c246399e4bffcb8?context=explore</p>
</div> 

&nbsp;

&nbsp;

### &#x1F530; Start Deployment in docker compose.

&nbsp;

<pre>
    ❯ ccat docker-compose.yml

            version: '3.7'
            
            services:
              localstack:
                image: localstack/localstack:2.0.0
                container_name: localstack_s3
                network_mode: bridge
                environment:
                  - DOCKER_HOST=unix:///var/run/docker.sock
                  - SERVICES=lambda,s3
                  - EDGE_PORT=4566
                  - PERSISTENCE=1
                  #- DEBUG=1
                  #- LS_LOG=trace
                  - DATA_DIR=/tmp/localstack
                  #- AWS_DEFAULT_REGION=ap-southeast-3
                ports:
                  - "4566:4566"
                volumes:
                  - ./localstack/localstack_data:/tmp/localstack
                  - ./localstack/localstack_libraries:/usr/lib/localstack    # static third-party packages installed into the container images
                  - ./localstack/localstack_root:/var/lib/localstack         # the LocalStack volume directory root
                  - "/var/run/docker.sock:/var/run/docker.sock"
</pre>

&nbsp;

Build.
<pre>
    ❯ docker-compose up

        +] Running 1/1
        ⠿ Container localstack_s3  Created                                                                                                                                                0.6s
        Attaching to localstack_s3
        localstack_s3  | SERVICES variable is ignored if EAGER_SERVICE_LOADING=0.
        localstack_s3  | 
        localstack_s3  | LocalStack version: 2.0.0
        localstack_s3  | LocalStack Docker container id: fb654c4bfa33
        localstack_s3  | LocalStack build date: 2023-03-30
        localstack_s3  | LocalStack build git hash: 7d80ee7f
        localstack_s3  | 
        localstack_s3  | 2024-03-30T02:55:23.840  WARN --- [  MainThread] localstack.deprecations    : DATA_DIR is deprecated (since 1.0.0) and will be removed in upcoming releases of LocalStack! Please use PERSISTENCE instead. The state will be stored in your LocalStack volume in the state/ directory.
        localstack_s3  | 2024-03-30T02:55:23.840  WARN --- [  MainThread] localstack.deprecations    : EDGE_PORT is deprecated (since 2.0.0) and will be removed in upcoming releases of LocalStack! This configuration will be migrated to GATEWAY_LISTEN
        localstack_s3  | 2024-03-30T02:55:24.501  WARN --- [-functhread3] hypercorn.error            : ASGI Framework Lifespan error, continuing without Lifespan support
        localstack_s3  | 2024-03-30T02:55:24.501  WARN --- [-functhread3] hypercorn.error            : ASGI Framework Lifespan error, continuing without Lifespan support
        localstack_s3  | 2024-03-30T02:55:24.507  INFO --- [-functhread3] hypercorn.error            : Running on https://0.0.0.0:4566 (CTRL + C to quit)
        localstack_s3  | 2024-03-30T02:55:24.507  INFO --- [-functhread3] hypercorn.error            : Running on https://0.0.0.0:4566 (CTRL + C to quit)
        localstack_s3  | 2024-03-30T02:55:24.536  INFO --- [  MainThread] localstack.utils.bootstrap : Execution of "start_runtime_components" took 605.29ms
        localstack_s3  | Ready.
</pre>

&nbsp;

File structure of the mounting directory.
<pre>
    ❯ tree -L 5 -a -I 'README.md|.DS_Store' ./localstack
        ├── localstack_config
        ├── localstack_data
        ├── localstack_libraries
        └── localstack_root
            ├── cache
            │   ├── machine.json
            │   ├── server.test.pem
            │   ├── server.test.pem.crt
            │   ├── server.test.pem.key
            │   └── service-catalog-2_0_0-1_29_97.pickle
            ├── lib
            ├── logs
            ├── state
            └── tmp

    9 directories, 5 files
</pre>

&nbsp;

&nbsp;

### &#x1F530; Testing with experimental stages.

- &#x2705; Command into the container.

        ❯ docker exec -it localstack_s3 /bin/bash

    <pre>
        # create bucket.
        ❯ awslocal s3 mb s3://testbucket
            make_bucket: testbucket

        # list buckets.
        ❯ awslocal s3 ls
            2024-03-30 03:06:58 testbucket
    </pre>

- &#x2705; Command used outside the container.
    <pre>
        ❯ exit
    </pre>
    <pre>
    # upload object
    ❯ aws --endpoint-url=http://localhost:4566 s3 cp ./tank.png s3://testbucket/tank.png
        upload: ./tank.png to s3://testbucket/tank.png

    # list objects
    ❯ aws --endpoint-url=http://localhost:4566 s3 ls s3://testbucket/ 
        2024-03-30 10:07:47      25600 tank.png
    </pre>

&nbsp;

**Restart docker compose:**

<pre>

    ❯ docker-compose restart localstack
        [+] Running 1/1
        ⠿ Container localstack_s3  Started  

    # list of files in the bucket
    ❯ aws --endpoint-url=http://localhost:4566 s3 ls s3://testbucket/
        An error occurred (NoSuchBucket) when calling the ListObjectsV2 operation: The specified bucket does not exist

</pre>

&nbsp;

&nbsp;

### &#x1F530; Conclusion.

The bucket and object data are reset after the container is restarted.

&nbsp;

&nbsp;

---

&nbsp;

<div align="center">
    <img src="./gambar-petunjuk/syukron.png" alt="syukron" style="display: block; margin: 0 auto;">
</div> 

&nbsp;

---

&nbsp;

&nbsp;