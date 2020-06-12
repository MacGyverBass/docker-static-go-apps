# Docker Images of Various Statically Compiled Go Applications

This is a repository with various applications written in Go and compiled statically in Docker.

These are compiled statically to only provide the bare minimum files within the final Docker image, thus keeping the final build size down and resulting in a more secure Docker container.

## List of Go Applications with Sources

- `dec-decode` - A iso.dec decoder, which implements the NASOS method of decoding .iso.dec files back into plain .iso files.  Source: [GitHub](https://github.com/sammiq/dec-decode)
- `lazydocker` - A simple terminal UI for both docker and docker-compose.  Source: [GitHub](https://github.com/jesseduffield/lazydocker)
- `webproc` - Wrap any program in a simple web-based user-interface.  Source: [GitHub](https://github.com/jpillora/webproc)
- `whaler` - Tool designed to reverse engineer docker images into the Dockerfile that created it.  Source: [GitHub](https://github.com/P3GLEG/Whaler)

Please review the documentation listed on the source pages for usage of the above applications.

## Examples of Use

Below are various examples for the above applications.  I will try to keep this list updated as more items are added.

The following example bind mounts a folder from the host to inside the container and procedes to supply a file to decode.  Remember to provide the folder path/name as it is mounted within the container.

```sh
docker run --rm -it \
 -v /my-folder/:/images/ \
 macgyverbass/dec-decode \
 /images/test.dec
```

The following example bind mounts the `docker.sock` file from the host and procedes to execute `lazydocker`.  Note that the application will ask if you want to enable anonymous reporting data to help improve lazydocker each time the Docker container is started.

```sh
docker run --rm -it \
 -v /var/run/docker.sock:/var/run/docker.sock \
 macgyverbass/lazydocker
```

The following example bind mounts the `docker.sock` file from the host and procedes to execute `whaler`.  Note that, as the program has documented, you may need to add `-sV=1.36` to the argument list, as shown below.

```sh
docker run --rm -it \
 -v /var/run/docker.sock:/var/run/docker.sock \
 macgyverbass/whaler \
 -sV=1.36 golang:alpine
```

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
