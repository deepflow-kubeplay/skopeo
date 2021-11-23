% skopeo-inspect(1)

## NAME
skopeo\-inspect - Return low-level information about _image-name_ in a registry.

## SYNOPSIS
**skopeo inspect** [*options*] _image-name_

## DESCRIPTION

Return low-level information about _image-name_ in a registry

_image-name_ name of image to retrieve information about

## OPTIONS

**--authfile** _path_

Path of the authentication file. Default is ${XDG\_RUNTIME\_DIR}/containers/auth.json, which is set using `skopeo login`.
If the authorization state is not found there, $HOME/.docker/config.json is checked, which is set using `docker login`.

**--cert-dir** _path_

Use certificates at _path_ (\*.crt, \*.cert, \*.key) to connect to the registry.

**--config**

Output configuration in OCI format, default is to format in JSON format.

**--creds** _username[:password]_

Username and password for accessing the registry.

**--daemon-host** _host_

Use docker daemon host at _host_ (`docker-daemon:` transport only)

**--format**, **-f**=*format*

Format the output using the given Go template.
The keys of the returned JSON can be used as the values for the --format flag (see examples below).

**--help**, **-h**

Print usage statement

**--no-creds**

Access the registry anonymously.

**--raw**

Output raw manifest or config data depending on --config option.
The --format option is not supported with --raw option.

**--registry-token** _Bearer token_

Registry token for accessing the registry.

**--retry-times**

The number of times to retry; retry wait time will be exponentially increased based on the number of failed attempts.

**--shared-blob-dir** _directory_

Directory to use to share blobs across OCI repositories.

**--tls-verify**=_bool_

Require HTTPS and verify certificates when talking to the container registry or daemon. Default to registry.conf setting.

**--username**

The username to access the registry.

**--password**

The password to access the registry.

**--no-tags**, **-n**=_bool_

Do not list the available tags from the repository in the output. When `true`, the `RepoTags` array will be empty.  Defaults to `false`, which includes all available tags.

## EXAMPLES

To review information for the image fedora from the docker.io registry:
```sh
$ skopeo inspect docker://docker.io/fedora
{
    "Name": "docker.io/library/fedora",
    "Digest": "sha256:a97914edb6ba15deb5c5acf87bd6bd5b6b0408c96f48a5cbd450b5b04509bb7d",
    "RepoTags": [
	"20",
	"21",
	"22",
	"23",
	"24",
	"heisenbug",
	"latest",
	"rawhide"
    ],
    "Created": "2016-06-20T19:33:43.220526898Z",
    "DockerVersion": "1.10.3",
    "Labels": {},
    "Architecture": "amd64",
    "Os": "linux",
    "Layers": [
	"sha256:7c91a140e7a1025c3bc3aace4c80c0d9933ac4ee24b8630a6b0b5d8b9ce6b9d4"
    ]
}
```

To inspect python from the docker.io registry and not show the available tags:
```sh
$ skopeo inspect --no-tags docker://docker.io/library/python
{
    "Name": "docker.io/library/python",
    "Digest": "sha256:5ca194a80ddff913ea49c8154f38da66a41d2b73028c5cf7e46bc3c1d6fda572",
    "RepoTags": [],
    "Created": "2021-10-05T23:40:54.936108045Z",
    "DockerVersion": "20.10.7",
    "Labels": null,
    "Architecture": "amd64",
    "Os": "linux",
    "Layers": [
        "sha256:df5590a8898bedd76f02205dc8caa5cc9863267dbcd8aac038bcd212688c1cc7",
        "sha256:705bb4cb554eb7751fd21a994f6f32aee582fbe5ea43037db6c43d321763992b",
        "sha256:519df5fceacdeaadeec563397b1d9f4d7c29c9f6eff879739cab6f0c144f49e1",
        "sha256:ccc287cbeddc96a0772397ca00ec85482a7b7f9a9fac643bfddd87b932f743db",
        "sha256:e3f8e6af58ed3a502f0c3c15dce636d9d362a742eb5b67770d0cfcb72f3a9884",
        "sha256:aebed27b2d86a5a3a2cbe186247911047a7e432b9d17daad8f226597c0ea4276",
        "sha256:54c32182bdcc3041bf64077428467109a70115888d03f7757dcf614ff6d95ebe",
        "sha256:cc8b7caedab13af07adf4836e13af2d4e9e54d794129b0fd4c83ece6b1112e86",
        "sha256:462c3718af1d5cdc050cfba102d06c26f78fe3b738ce2ca2eb248034b1738945"
    ],
    "Env": [
        "PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
        "LANG=C.UTF-8",
        "GPG_KEY=A035C8C19219BA821ECEA86B64E628F8D684696D",
        "PYTHON_VERSION=3.10.0",
        "PYTHON_PIP_VERSION=21.2.4",
        "PYTHON_SETUPTOOLS_VERSION=57.5.0",
        "PYTHON_GET_PIP_URL=https://github.com/pypa/get-pip/raw/d781367b97acf0ece7e9e304bf281e99b618bf10/public/get-pip.py",
        "PYTHON_GET_PIP_SHA256=01249aa3e58ffb3e1686b7141b4e9aac4d398ef4ac3012ed9dff8dd9f685ffe0"
    ]
}
```

```
$ /bin/skopeo inspect --config docker://registry.fedoraproject.org/fedora --format "{{ .Architecture }}"
amd64
```

```
$ /bin/skopeo inspect --format '{{ .Env }}' docker://registry.access.redhat.com/ubi8
[PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin container=oci]
```

# SEE ALSO
skopeo(1), skopeo-login(1), docker-login(1), containers-auth.json(5)

## AUTHORS

Antonio Murdaca <runcom@redhat.com>, Miloslav Trmac <mitr@redhat.com>, Jhon Honce <jhonce@redhat.com>
