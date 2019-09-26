# Hosting a Console Application in Docker

## Set-up

1. Download and install [Docker](https://docs.docker.com/docker-for-windows/install/ "Docker Installation").
2. Login and switch to Windows Containers.
   ![Switch to Windows Containers](../blob/master/media/docker.png)
3. Go to Windows 'System Information' (Start -> System Information) and retrieve the version of Windows you are using under 'Version'.
   ![System Information](../blob/master/media/systeminfo.png)
4. Search for your Windows version on [Microsoft's Windows Server Core Images section](https://hub.docker.com/_/microsoft-windows-servercore?tab=description "Microsoft servercore") for using the correct image.
   For example, my version is 17134 so I search for 17134:
   ![System Information](../blob/master/media/version.png)
5. Any tag on the left most column can be used as long as the OsVersion lines up with your computer's version, for this example I use the tag '1803' as it was the simplest one and lined up with my version of 17134.
   You **must** use the correct tag otherwise it will fail to pull the image.
6. If you do not need to run the container on a set Ip Address, skip the Docker Network step and omit the --network and --ip options during container creation.

## Create the Docker Network

If you do not need to run the container on a set Ip Address, skip this step and omit the --network and --ip options during container creation.

The command to create a docker network is:

```shell
docker network create -d transparent --subnet=<subnet> --gateway=<gateway> <network name>
```

For example:

```shell
docker network create -d transparent --subnet=192.168.1.0/25 --gateway=192.168.1.100 Network
```

## Create the Container

If you do not need to run the container on a set Ip Address, omit the --network and --ip options. If you do, make sure you do the Docker Network step first.

The command to create the container is:

```shell
docker run -it --name <container name> --network=<network name> --ip <ip> mcr.microsoft.com/windows/servercore:<servercore version>
```

To get your servercore version, see Set-up.

For example:

```shell
docker run -it --name container --network=Network --ip 192.168.1.22 mcr.microsoft.com/windows/servercore:1803
```

For the rest of the example, any places where you see 'container' you can replace with your container name.

## Running the Container with Powershell

The command to starting running the container with Powershell is:

```shell
docker exec -it container powershell
```

If you get an error that the container is not running, start the container:

```shell
docker start container
```

## Start a Console Application in the Container

Copy all files needed to run the application into the docker container, then run the executable.

1. From inside the docker container, I created a directory to contain all the files needed:

   ```shell
   mkdir Application
   ```

2. Close the container and then stop it from the host:

   ```shell
   docker stop container
   ```

3. Move all files into the container from the solution's /bin/Release directory (or /bin/Debug if you do not have a Release). It is easiest to enter the directory, then move all files to your newly created directory in the container (make sure to put the path in quotations ("") if there are spaces in it):

   ```shell
   cd <path to your solution>/bin/Release
   docker cp . container:C:/Application
   ```

   If you ever get a "filesystem operations against a running container are not supported" error, then make sure to stop the container (see step 2 above).

4. Start and re-enter your container (see Running the Container with Powershell).
5. From inside the container, navigate to your directory and check if all files were added:

   ```shell
   cd Application
   ls
   ```

6. Run the executable for your solution:

   ```shell
   ./Application.exe
   ```

## Other useful commands

### Show all containers (remove the -a to only show running ones)

```shell
docker container ls -a
```

### Remove a container

```shell
docker rm <container name>
```

### Rename a container

```shell
docker rename <current container name> <new container name>
```
