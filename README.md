# Docker Images of Various Statically Compiled Go Applications

This is a repository with various applications written in Go and compiled statically in Docker.

These are compiled statically to only provide the bare minimum files within the final Docker image, thus keeping the final build size down and resulting in a more secure Docker container.

## List of Go Applications with Sources

- `dec-decode` - A iso.dec decoder, which implements the NASOS method of decoding .iso.dec files back into plain .iso files.  Source: [GitHub](https://github.com/sammiq/dec-decode)
- `dive` - A tool for exploring a docker image, layer contents, and discovering ways to shrink the size of your Docker/OCI image.  Source: [GitHub](https://github.com/wagoodman/dive)
- `docker-webui` - A web interface for Docker, providing various functions for reviewing and controlling containers.  Source: [GitHub](https://github.com/hajimeo/docker-webui)
- `lazydocker` - A simple terminal UI for both docker and docker-compose.  Source: [GitHub](https://github.com/jesseduffield/lazydocker)
- `pasta` - Pastebin-like web-server.  Source: [GitHub](https://github.com/starius/pasta)
- `reg` - Docker registry v2 command line client and repo listing generator with security checks.  Source: [GitHub](https://github.com/genuinetools/reg)
- `skopeo` - Work with remote images registries - retrieving information, images, signing content.  Source: [GitHub](https://github.com/containers/skopeo)
- `webproc` - Wrap any program in a simple web-based user-interface.  Source: [GitHub](https://github.com/jpillora/webproc)
- `whaler` - Tool designed to reverse engineer docker images into the Dockerfile that created it.  Source: [GitHub](https://github.com/P3GLEG/Whaler)

Please review the documentation listed on the source pages for usage of the above applications.

---

## Examples of Use

Below are various examples for the above applications.  I will try to keep this list updated as more items are added.

### Example for dec-decode

The following example bind mounts a folder from the host to inside the container and procedes to supply a file to decode.  Remember to provide the folder path/name as it is mounted within the container.

```sh
docker run --rm -it \
 -v /my-folder/:/images/ \
 macgyverbass/dec-decode \
 /images/test.dec
```

### Example for dive

The following example bind mounts the `docker.sock` file from the host and uses "alpine:latest" as the image to review.

```sh
docker run --rm -it \
 -v /var/run/docker.sock:/var/run/docker.sock \
 macgyverbass/dive \
 alpine:latest
```

### Example for docker-webui

The following example bind mounts the `docker.sock` file from the host and forwards port 9000 to the host.

```sh
docker run --rm -it \
 -v /var/run/docker.sock:/var/run/docker.sock \
 -p 9000:9000/tcp \
 macgyverbass/docker-webui
```

### Example for lazydocker

The following example bind mounts the `docker.sock` file from the host and procedes to execute `lazydocker`.  Note that the application will ask if you want to enable anonymous reporting data to help improve lazydocker each time the Docker container is started.

```sh
docker run --rm -it \
 -v /var/run/docker.sock:/var/run/docker.sock \
 macgyverbass/lazydocker
```

### Examples for pasta

The following example bind mounts a folder from the host to inside the container for storing the data files and with port 8042 forwarded to the host.  Note that when you launch `pasta`, it will ask for a secret (e.g. "adam bravo charlie david edward frank george henry ida john king lincoln" - note that this is just an example) - if you want to generate a secret using `pasta`, you can pass `-gen-secret` as also shown in the next example further below.

```sh
docker run --rm -it \
 -v /my-folder/:/db/ \
 -p 8042:8042/tcp \
 macgyverbass/pasta
```

As mentioned before, `pasta` can generate a secret phrase for you using the below command.  (The output text can then be stored in a file for use with the `-secret-file` argument shown in the next example further below.)

```sh
docker run --rm -it \
 macgyverbass/pasta \
 -gen-secret
```

Additionally, a text file (`/my-folder/secret.txt` in this example) with a secret phrase may be loaded using the `-secret-file` argument as shown below:  (Note that, for simplicity, the text file is located in the same host folder as the data files, but it can also be stored elsewhere and mounted within the container, with the new mounted location passed accordingly.)  This will allow the binary to launch without user input at startup.

```sh
docker run --rm -it \
 -v /my-folder/:/db/ \
 -p 8042:8042/tcp \
 macgyverbass/pasta \
 -secret-file /db/secret.txt
```

### Example for reg

The following example runs `reg` with the command `tags` for the image `alpine`.  Many other commands are available, so please review the official [GitHub Usage Section](https://github.com/genuinetools/reg#usage) for more commands/options and examples.

```sh
docker run --rm -it \
 macgyverbass/reg \
 tag alpine
```

### Example for skopeo

The following example runs `skopeo` with the `inspect` argument to show properties of `fedora:latest`.  (This example is based upon the example from the official [GitHub Examples Section](https://github.com/containers/skopeo#show-properties-of-fedoralatest).)

```sh
docker run --rm -it \
 macgyverbass/skopeo \
 inspect docker://registry.fedoraproject.org/fedora:latest
```

### Example for whaler

The following example bind mounts the `docker.sock` file from the host and procedes to execute `whaler`.  Note that, as the program has documented, you may need to add `-sV=1.36` to the argument list, as shown below.

```sh
docker run --rm -it \
 -v /var/run/docker.sock:/var/run/docker.sock \
 macgyverbass/whaler \
 -sV=1.36 golang:alpine
```

### Examples for webproc

Note that `webproc` is best used when it is added to another Docker image, as there are no other binaries within it's own image.  For example, you may create a Dockerfile like below using `webproc`, which will `ping 8.8.8.8` (a simple ping to Google DNS 8.8.8.8) using `webproc`.

```Dockerfile
FROM alpine:latest

COPY --from=macgyverbass/webproc:latest /webproc /webproc

CMD  ["/webproc", "ping", "8.8.8.8"]
```

And then running the above image ("webproc-test" in this example) from the Dockerfile above with port 8080 forwarded to the host as shown here:

```sh
docker run --rm -it \
 -p 8080:8080/tcp \
 webproc-test
```

In the above example, opening a browser with `http://[Your Docker Host IP]:8080/` will display the webpage front-end of `webproc` and the output of the command being executed with the ability to restart the process.  Note that restarting the process from the webpage does not restart the entire container, just the process passed to `webproc` itself.

Your usage may differ, as the above is just an example of its use.

## Other Ways of Using the Docker Images

As noted in the previous section, the Docker images can also be copied into a new image by referencing them in the Dockerfile.

For example, the below Dockerfile example shows how to copy in just the static binary `dec-decode` into your own Docker image to be use for another project.  In this way, you don't have to execute the Docker image directly, but instead use the compiled static binary within your own image.

```Dockerfile
FROM alpine:latest

COPY --from=macgyverbass/dec-decode:latest /dec-decode /dec-decode

COPY your-scripts/ /scripts/

CMD  ["/scripts/my-script.sh"]
```

Again, your uses will differ from the above example, but it can be used as a guide for building your own Docker image using the static binary.
