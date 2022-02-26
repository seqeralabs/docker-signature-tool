# docker-signature-tool

### Summary  

Docker image manifest following the specification [Version 2 - Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/#signed-manifests)
can contain a cryptographic signature to assess the integrity of the image. See the following example: 

```
{
   "name": "hello-world",
   "tag": "latest",
   "architecture": "amd64",
   "fsLayers": [
      {
         "blobSum": "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef"
      }
   ],
   "history": [
      {
         "v1Compatibility": ".."
      }
   ],
   "schemaVersion": 1,
   "signatures": [
      {
         "header": {
            "jwk": {
               "crv": "P-256",
               "kid": "OD6I:6DRK:JXEJ:KBM4:255X:NSAA:MUSF:E4VM:ZI6W:CUN2:L4Z6:LSF4",
               "kty": "EC",
               "x": "3gAwX48IQ5oaYQAYSxor6rYYc_6yjuLCjtQ9LUakg4A",
               "y": "t72ge6kIA1XOjqjVoEOiPPAURltJFBMGDSQvEGVB010"
            },
            "alg": "ES256"
         },
         "signature": "XREm0L8WNn27Ga_iE_vRnTxVMhhYY0Zst_FfkKopg6gWSoTOZTuW4rK0fg_IqnKkEKlbD83tD46LKEGi5aIVFg",
         "protected": "eyJmb3JtYXRMZW5ndGgiOjY2MjgsImZvcm1hdFRhaWwiOiJDbjAiLCJ0aW1lIjoiMjAxNS0wNC0wOFQxODo1Mjo1OVoifQ"
      }
   ]
}
```

This mechanism has been deprecated, and replaced by Docker Notary v1 / content trust (see [here](https://stackoverflow.com/questions/71188800/how-to-create-and-validate-docker-signed-manifests?noredirect=1#comment125837443_71188800)). 

However, when pulling an old Docker image still is using this manifest format, it checks the signature and prevents the execution if it's not valid. Container collections such as [Biocontainer](https://biocontainers.pro/) hold a large number of images using this manifest format. 

### Goal 

The goal of this project is to write a command line tool that is able to:
1. Strip the signature without altering the (remanining) mainifest payload.
2. Generate a new signature for an image manifest.
3. Validate the signature for a given signed manifest. 

Stating the documentation and comments on Stackoverflow it should be doable using the Docker [trustlib](https://stackoverflow.com/questions/71188800/how-to-create-and-validate-docker-signed-manifests?noredirect=1#comment125840233_71188800). 

### Reference  

As reference image manifest for testing it can be used the following container `quay.io/biocontainers/fastqc:0.11.9--0`. 

The response for a pull request is the following 

```
HTTP/1.1 200 OK
Date: Sun, 20 Feb 2022 10:07:02 GMT
Content-Type: application/vnd.docker.distribution.manifest.v1+prettyjws
Content-Length: 7636
Connection: close
Server: nginx/1.14.1
Docker-Content-Digest: sha256:319b8d4eca0fc0367d192941f221f7fcd29a6b96996c63cbf8931dbb66e53348
X-Frame-Options: DENY
Strict-Transport-Security: max-age=63072000; preload

{
   "schemaVersion": 1,
   "name": "biocontainers/fastqc",
   "tag": "0.11.9--0",
   "architecture": "amd64",
   "fsLayers": [
      {
         "blobSum": "sha256:6d92b3a49ebfad5fe895550c2cb24b6370d61783aa4f979702a94892cbd19077"
      },
      {
         "blobSum": "sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4"
      },
      {
         "blobSum": "sha256:10c3bb32200bdb5006b484c59b5f0c71b4dbab611d33fca816cd44f9f5ce9e3c"
      },
      {
         "blobSum": "sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4"
      },
      {
         "blobSum": "sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4"
      },
      {
         "blobSum": "sha256:dfda3e01f2b637b7b89adb401f2f763d592fcedd2937240e2eb3286fabce55f0"
      },
      {
         "blobSum": "sha256:d2ba336f2e4458a9223203bf17cc88d77e3006d9cbf4f0b24a1618d0a5b82053"
      },
      {
         "blobSum": "sha256:7ff999a2256f84141f17d07d26539acea8a4d9c149fefbbcc9a8b4d15ea32de7"
      },
      {
         "blobSum": "sha256:00cf8b9f3d2a08745635830064530c931d16f549d031013a9b7c6535e7107b88"
      },
      {
         "blobSum": "sha256:3aaade50789a6510c60e536f5e75fe8b8fc84801620e575cb0435e2654ffd7f6"
      },
      {
         "blobSum": "sha256:77c6c00e8b61bb628567c060b85690b0b0561bb37d8ad3f3792877bddcfe2500"
      },
      {
         "blobSum": "sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4"
      }
   ],
   "history": [
      {
         "v1Compatibility": "{\"architecture\":\"amd64\",\"config\":{\"Hostname\":\"\",\"Domainname\":\"\",\"User\":\"\",\"AttachStdin\":false,\"AttachStdout\":false,\"AttachStderr\":false,\"Tty\":false,\"OpenStdin\":false,\"StdinOnce\":false,\"Env\":[\"PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin\"],\"Cmd\":[\"/bin/sh\"],\"Image\":\"\",\"Volumes\":null,\"WorkingDir\":\"\",\"Entrypoint\":null,\"OnBuild\":null,\"Labels\":{}},\"container\":\"4be9f6b4406ec142e457fd7c43ff338511ab338b33585c30805ba2d5d3da29e3\",\"container_config\":{\"Hostname\":\"4be9f6b4406e\",\"Domainname\":\"\",\"User\":\"\",\"AttachStdin\":false,\"AttachStdout\":false,\"AttachStderr\":false,\"Tty\":false,\"OpenStdin\":false,\"StdinOnce\":false,\"Env\":[\"PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin\"],\"Cmd\":[\"/bin/sh\"],\"Image\":\"bgruening/busybox-bash:0.1\",\"Volumes\":null,\"WorkingDir\":\"\",\"Entrypoint\":null,\"OnBuild\":null,\"Labels\":{}},\"created\":\"2020-01-24T15:39:30.564518517Z\",\"docker_version\":\"17.09.0-ce\",\"id\":\"f235879f79194a5e3d4b10c3b714c36669e8e98160ba73bd9b044fdec624ceaf\",\"os\":\"linux\",\"parent\":\"b7c0567175be2a551a8ed4e60d695d33347ebae5d8cfc4a5d0381e0ce3c34222\"}"
      },
      {
         "v1Compatibility": "{\"id\":\"b7c0567175be2a551a8ed4e60d695d33347ebae5d8cfc4a5d0381e0ce3c34222\",\"parent\":\"32dbc9f4b6f9f15dfcce38773db21d7aadfc059242a821fb98bc8cf0997d05ce\",\"created\":\"2016-05-09T06:21:02.266124295Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) CMD [\\\"/bin/sh\\\" \\\"-c\\\" \\\"/bin/bash\\\"]\"]},\"author\":\"Bjoern Gruening \\u003cbjoern.gruening@gmail.com\\u003e\",\"throwaway\":true}"
      },
      {
         "v1Compatibility": "{\"id\":\"32dbc9f4b6f9f15dfcce38773db21d7aadfc059242a821fb98bc8cf0997d05ce\",\"parent\":\"ce56e62b24426c8b57a4e3f23fcf0cab885f0d4c7669f2b3da6d17b4b9ac7268\",\"created\":\"2016-05-09T06:21:02.050926818Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) ADD file:8583f81843640f66efa0cce8dc4f49fd769ed485caa0678ef455aa65876b03e2 in /bin/bash\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"ce56e62b24426c8b57a4e3f23fcf0cab885f0d4c7669f2b3da6d17b4b9ac7268\",\"parent\":\"03cffc43c0a5cf4268bbf830cf917e5d089f74e03609d1b3c79522e6043d769e\",\"created\":\"2016-05-08T21:00:29.07168933Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) MAINTAINER Bjoern Gruening \\u003cbjoern.gruening@gmail.com\\u003e\"]},\"throwaway\":true}"
      },
      {
         "v1Compatibility": "{\"id\":\"03cffc43c0a5cf4268bbf830cf917e5d089f74e03609d1b3c79522e6043d769e\",\"parent\":\"0cc77c3b7a87611a9e75e6a2fccd25ed3c2ca4802a4b31717b0af64080c59929\",\"created\":\"2015-08-11T21:02:14.747794245Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) CMD [\\\"/bin/sh\\\"]\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"0cc77c3b7a87611a9e75e6a2fccd25ed3c2ca4802a4b31717b0af64080c59929\",\"parent\":\"0f7d738a9dc69d3820b5f7410d42053d293b02d08e8e11a65d82aa1894bd704e\",\"created\":\"2015-08-11T21:02:14.521653891Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c opkg-cl install http://downloads.openwrt.org/snapshots/trunk/x86/64/packages/base/libc_1.1.10-1_x86_64.ipk\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"0f7d738a9dc69d3820b5f7410d42053d293b02d08e8e11a65d82aa1894bd704e\",\"parent\":\"29e08e6a06e056bcd283f52a93b4c81b6382c0607bcdf5056c0c6b97e98b93a4\",\"created\":\"2015-08-11T21:02:13.058801425Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c opkg-cl install http://downloads.openwrt.org/snapshots/trunk/x86/64/packages/base/libgcc_4.8-linaro-1_x86_64.ipk\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"29e08e6a06e056bcd283f52a93b4c81b6382c0607bcdf5056c0c6b97e98b93a4\",\"parent\":\"5174b5bc351e5d990a556e34932388f9f034e057631cfd0cacc47347c3607d57\",\"created\":\"2015-08-11T21:02:12.018991461Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) ADD file:d01ddbb13c1e847e7064ac35cbdc5c840c3a64c9aef2552aec5315a5375da312 in /lib/functions.sh\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"5174b5bc351e5d990a556e34932388f9f034e057631cfd0cacc47347c3607d57\",\"parent\":\"9b94b4d44827aaf8b87b0f5e5f65ef391f15dbd19575ff13296c3ad856e13ace\",\"created\":\"2015-08-11T21:02:11.542704863Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) ADD file:e2c3819e14cb4b8d2fff4ba6b2e2f49992788470d18d18f4aba3f3aaf2b30d40 in /usr/bin/opkg-install\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"9b94b4d44827aaf8b87b0f5e5f65ef391f15dbd19575ff13296c3ad856e13ace\",\"parent\":\"84249641cd2c5dd2da40ca5c7e20cc1429d821edc6172559380c6ca2c30bb356\",\"created\":\"2015-08-11T21:02:11.097787026Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) ADD file:1fb1c8c23666e2dc3a1cfe388868f16a3b46cbfa5b2dfd5b43382adfd1599527 in /etc/opkg.conf\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"84249641cd2c5dd2da40ca5c7e20cc1429d821edc6172559380c6ca2c30bb356\",\"parent\":\"3690474eb5b4b26fdfbd89c6e159e8cc376ca76ef48032a30fa6aafd56337880\",\"created\":\"2015-08-11T21:02:10.601706052Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) ADD file:317a8c7f54c369601633fa49b1820a28778446f3a253eba0db0ef3fdb71461a4 in /\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"3690474eb5b4b26fdfbd89c6e159e8cc376ca76ef48032a30fa6aafd56337880\",\"created\":\"2015-08-11T21:02:10.275489246Z\",\"container_config\":{\"Cmd\":[\"/bin/sh -c #(nop) MAINTAINER Jeff Lindsay \\u003cprogrium@gmail.com\\u003e\"]}}"
      }
   ],
   "signatures": [
      {
         "header": {
            "jwk": {
               "crv": "P-256",
               "kid": "H4I5:ZTAM:GNQK:AM3G:HF4X:RFAK:U67G:GHDB:L4S3:EBKB:Y7Z2:QPXP",
               "kty": "EC",
               "x": "ZAM8UZLnMkeRrrK2J81Vv2Whi2yHt3aevb1fKOqZenQ",
               "y": "J6Oeam_YkFuANSPxVYTFq8iZjoP7JCvIrFlABrD1JCA"
            },
            "alg": "ES256"
         },
         "signature": "4-yAR3tVSQHhFfu_LxWkuVoAW3eqeBkIK1azWYH_-4N4pKvkJCoS2CjUDzcUBNkeul38pUocgLSUUmHiSdqjUA",
         "protected": "eyJmb3JtYXRMZW5ndGgiOjY5ODksImZvcm1hdFRhaWwiOiJDbjAiLCJ0aW1lIjoiMjAyMC0wMS0yNFQxNTo0MToxNloifQ"
      }
   ]
}
```

### Links
- https://docs.docker.com/registry/spec/manifest-v2-1
- https://github.com/opencontainers/image-spec/blob/v1.0.0/config.md
- https://gist.github.com/aaronlehmann/b42a2eaf633fc949f93b#id-definitions-and-calculations
- https://github.com/moby/moby/blob/master/image/spec/v1.md
- https://stackoverflow.com/questions/71188800/how-to-create-and-validate-docker-signed-manifests?noredirect=1#comment125840233_71188800
- https://github.com/containers/skopeo/issues/526#issuecomment-411901330 <<< 
- https://wstutorial.com/misc/verify-jws-signature.html
- https://www.scottbrady91.com/jose/jwts-which-signing-algorithm-should-i-use
- https://blog.frickjack.com/2021/04/sign-and-verify-jwt-with-es256.html
- https://www.jfrog.com/jira/browse/RTFACT-24490
- https://github.com/opencontainers/image-spec/issues/743
- https://gist.github.com/aaronlehmann/b42a2eaf633fc949f93b#id-definitions-and-calculations
