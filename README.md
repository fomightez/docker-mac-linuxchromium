[![](https://images.microbadger.com/badges/image/fomightez/chromium.svg)](https://microbadger.com/images/fomightez/chromium "Get your own image badge on microbadger.com")

Linux Chromium to Mac Display using Docker
===================================

See associated Docker image at  
https://hub.docker.com/r/fomightez/chromium/


Image for a container for Linux chromium with all dependencies that can be tunneled to Mac Desktop
-------------------------------

[Dockerfile](https://github.com/fomightez/docker-mac-linuxchromium)

Dockerfile for the image adapted from [here](https://github.com/docker/docker/tree/b248de7e332b6e67b08a8981f68060e6ae629ccf/contrib/desktop-integration/chromium) because that one worked whereas the version that is presently at the [source of the other images](https://hub.docker.com/u/jess/) I am pulling doesn't work. Specifically, since present version of this [particular one](https://github.com/jessfraz/dockerfiles/blob/master/chromium/Dockerfile) seems to fail at this time. I only changed the comments section of the original Dockerfile.

Process to use and examples based on following [Bring Linux apps to the Mac Desktop with Docker](http://blog.alexellis.io/linux-desktop-on-mac/) and [How to run a Linux GUI application on OSX using Docker](http://kartoza.com/en/blog/how-to-run-a-linux-gui-application-on-osx-using-docker/) and Jessie Frazelle's [Docker Containers on the Desktop](https://blog.jessfraz.com/post/docker-containers-on-the-desktop/).

Usage
--------

1. Install the necessary software following: [Bring Linux apps to the Mac Desktop with Docker](http://blog.alexellis.io/linux-desktop-on-mac/) and [How to run a Linux GUI application on OSX using Docker](http://kartoza.com/en/blog/how-to-run-a-linux-gui-application-on-osx-using-docker/) . Essentially you'll need working Docker, socat, and XQuartz.

	Notes:
	* I am doing this on a computer running OSX 10.9.5 (Mavericks), and so I used Docker Toolbox from Docker.com. Docker.com suggests their newer software, Docker for Mac, if you can run it.
	* I found the solution to install XQuartz from [How to run a Linux GUI application on OSX using Docker](http://kartoza.com/en/blog/how-to-run-a-linux-gui-application-on-osx-using-docker/) worked for me. I found the `homebrew` approach to not work for me. YMMV. This is a rather lengthy install.
	* `homebrew` did work for installing `socat`, but I had to use Jikku Jose's answer [here](http://stackoverflow.com/questions/26647412/homebrew-could-not-symlink-usr-local-bin-is-not-writable) to allow brew to make the symlinks.

2. Now you need to do three steps of preparation.

	First, start `socat` up with the following command in a fresh terminal window

		socat TCP-LISTEN:6000,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\"

	This will create a tunnel from an open X11 port (6000) through to the local UNIX socket where XQuartz will be listening for connections.

	**Importantly**: this will cause that terminal window to "hang" in a running, yet non-responsive state as the tunnel runs. Leave that terminal window open like that, and move to another one for all subsequent terminal use.

3. Next, start up XQuartz from the LaunchPad and toggle the settings that [Kartoza suggests](http://kartoza.com/en/blog/how-to-run-a-linux-gui-application-on-osx-using-docker/). Specifically,in X11 preferences in XQuartz, in the `security` tab, check both boxes as illustrated [here](http://kartoza.com/en/blog/how-to-run-a-linux-gui-application-on-osx-using-docker/). Kartoza says you can close the terminal that pops up in XQuartz at startup.

	You can leave those two situated open from now on. I wouldn't close those until you are completely through trying to stream output from out of your Linux container to your Mac desktop.

4. You need to get an IP address from your local Mac that you'll need to use for the `docker run` commands below, you need to replace the part in from of the `:0` in `192.168.99.1:0` in the commands below with the second address listed when you issue the command

		ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}'

5. That completes the preparation and analysis on your Mac system. Now you are ready to step through actually running the Linux container and piping it out to your Mac screen.

	Now for your first test, I would suggest something simple. I found the examples from [Bring Linux apps to the Mac Desktop with Docker](http://blog.alexellis.io/linux-desktop-on-mac/) and [How to run a Linux GUI application on OSX using Docker](http://kartoza.com/en/blog/how-to-run-a-linux-gui-application-on-osx-using-docker/) to be overly complex for first attempts. I suggest using the gparted docker image an established Docker image first with a simple, graphical-user-interface-based program to check.

	In your terminal window enter

		docker run -e DISPLAY=192.168.99.1:0 --name gparted jess/gparted

	If all goes well, the focus should go to XQuartz where you will see Linux's gparted running. See `Troubleshooting` below if something else happened.

	Select `Quit` from the file menu there to shut the container down. Back in the terminal window, you'll see the normal command line prompt return.

	Another check is to use Inkscape, that command is

		docker run -it -e DISPLAY=192.168.99.1:0 --name ink jess/inkscape

	*Remember to replace the IP address in the command with yours, keeping the `:0` after it.*

	Select `Quit` from the file menu there to shut the container down. Back in the terminal window, you'll see the normal command line prompt return.

6. Now use this Docker image to try Chromium.

		docker run -e DISPLAY=192.168.99.1:0 -v $HOME/Downloads:/root/Downloads --name chrome fomightez/chromium

	*Remember to replace the IP address in the command with yours, keeping the `:0` after it.*

	Downloads will go to the local Mac `Downloads` directory because of the volume-binding command line flag.

	Select `Quit` from the file menu there to shut the container down. Back in the terminal window, you'll see the normal command line prompt return.


Troubleshooting and cleaning up
-------------------------------

If you try to run a Docker image unsuccessfully or successfully, it may still create a visible entry you'll see when you enter `docker ps -a` to list docker jobs.

Therefore if you get an error that begins

	docker: Error response from daemon: Conflict. The container name...

you'll need to remove it before you can create a new one. Issue the command

	docker rm -f id_or_name

where `id_or_name` is the name or the first three or four characters of the `CONTAINER ID`.

Additionally, you may wish to remove images that you see when you issue the command `docker images`.
The command is

	docker rmi id_or_name

where `id_or_name` is the name or the first three or four characters of the `IMAGE ID  `.
Check you results with

	docker ps -a
	docker images


Related
-------

[First Touch Down with Docker for Mac](https://blog.hypriot.com/post/first-touch-down-with-docker-for-mac/) says you can send programs running from in a raspberry pi container to Mac OS display

[Docker Containers on the Desktop](https://blog.jessfraz.com/post/docker-containers-on-the-desktop/) says you can send program running in a Linux container out to display on a native Linux computer

I imagine ultimately Windows will be possible in this ability to go between systems will include Windows-based systems, I understand [Docker in Windows development is nascent and only works on a few versions for now](https://stefanscherer.github.io/run-linux-and-windows-containers-on-windows-10/).
