[?] This link (https://sdv-blueprints.eclipse.dev/docs/companion-application/) should be the first thing to be referenced inside the repo in order to have a clear path to follow.
	e.g. For a full guide from start to finish visit this link:https://sdv-blueprints.eclipse.dev/docs/companion-application/

[?] Add Getting Started section and move high level overview (step 1-3) inside it.
	GETTING STARTED
	The following pages show how to execute the explained setup using Eclipse Velocitas and Eclipse Leda 0.1.0-M2:

	On a high level, you need to perform the following steps described in more detail in this guide:

	1. Use the Eclipse Velocitas template repository to develop, build and deploy your version of the seat adjuster example.

	2.Run Leda with QEMU (recommended) and manage the Eclipse Kanto container runtime to deploy your seat adjuster application.

	3.Test the deployed setup by interacting with the seat adjuster to change the seat position.


[?] There are too many links to other pages e.g. Run leda AS CONTAINER or .... => this could be refactored to => 
	2. Run Eclipse Leda with QEMU (recommended). For other options like Linux, physical hardware, etc.. find out more at this link: https://eclipse-leda.github.io/leda/docs/general-usage/


** DOCKER & DEVCONTAINERS TROUBLESHOOTING **

=> If you are having issues with reopening the project in a DevContainer e.g. "Is Docker deamon not running"
	you can try fixing this initial issue by opening VSCode with sudo rights.
	To acheive this, close your VSCode window and run this command in the terminal:

	```
	sudo code --user-data-dir="~/.vscode-root"
	```

=> If the DevContainer fails to open, make sure that docker and docker-desktop is installed correctly and running, with the following commands:

	```
	docker --version
	sudo systemctl status docker
	docker run hello-world
	```

=> If the problems persist, you might have a misconfiguration of your Docker.

	You might also be in the wrong Docker context, in order to debug, list them:

	```
	docker context list
	```

	If you see multiple, try switching to them (try switching to the default one)
	Example:
	```
	docker context use default
	```

	Once you have switched to a different context, try reopening in DevContainer.


=> If nothing works, you can try purging and reinstalling the entire Docker:

	```
	sudo apt-get purge docker-ce docker-ce-cli containerd.io
	sudo rm -rf /etc/docker/ /var/lib/docker/
	```

	To avoid conflicts during the reinstallation, remove Docker repositories:

	```
	sudo rm /etc/apt/sources.list.d/docker.list
	sudo apt-get update
	```

	Install Docker CLI:

	```
	sudo apt-get update
	sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
	sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
	sudo apt-get update
	sudo apt-get install -y docker-ce docker-ce-cli containerd.io
	sudo systemctl start docker
	sudo systemctl enable docker
	```

	Now it's time for Docker Desktop:

	Download latest Docker Desktop .deb package
	Open it in the terminal and run:
	```
	sudo dpkg -i <docker-desktop>.deb
	```

	Final check to see if everything was reinstalled correctly:

	```
	docker --version
	sudo systemctl status docker
	docker run hello-world
	```

** GENERAL TROUBLESHOOTING **

[?] After running the Pre Commit Action it is unclear if git commit is needed to be ran before pushing. Currently, there are some critical files that are untracked and others that require a commit.


=> During the process of Publishing the Release, make sure to add a meaningful tag or you will be blocked by the following error: 

	> There was an error creating your Release: tag name can't be blank, tag name is not well-formed, published releases must have a valid tag.


=> If any of the workflow (CI, Multiarch, Release) fail there is likely a misconfiguration of the permissions in your github repo.
	Firstly, make sure that the repo is private.
	Once you have excluded that problem, you can go to your GitHub settings, tokens and then inspect the token assigned to this repo.
	Retry the run the workflow. If it still is not wokring 

=> Please make sure to wait 10 seconds after pressing "r" in order to remove the container.
	NOTE: R from <[R]emove> is actually lower-case r

=>IMPORTANT: if you are getting a 401 Unauthorized error you must first navigate to Code -> Package -> Package Settings
	Scroll to the bottom and within the Danger Zone change the visibility if it is set to private to public. 
	kanto-cm create will now correctly create the container from your application.


PROBLEMS:

Numbering and layout should be clearer. Example: the high level steps 1 to 4 are helpful but they seem to separated to the succeeding section where instead they are the expansion of each section.


In this page:
https://sdv-blueprints.eclipse.dev/docs/companion-application/deploy-seat-adjuster
At this section: Add manifest for seat adjuster
There is the following instruction:
	As an alternative to using kanto-cm, you can add a container manifest to the directoy watched by the kanto-auto-deployer (data/var/containers/manifests).

I am unsure how to access that directory as it is not present inside Leda.


[?] Replace <YOUR_OR>/seat-adjuster-app:latest with the name of the image that you created if all the workflow succeeded. 
	
	You can find it in Code -> Packages and it will look similar to this:

	ghcr.io/noci0001/my-seat-adjuster/seatadjuster:1



[?] In "Add manifest for seat-adjuster" it's written:
	"and copy the manifest from below. You can save the file with strg+s and close the window with strg+q. You can create the file on the development machine and copy it via scp too:"
	It could be unclear for different keyboards layout what strg+s mean so I would add a (or ctrl+s) like so:
	"and copy the manifest from below. You can save the file with strg+s (or ctrl+s) and close the window with strg+q (or ctrl+q). You can create the file on the development machine and copy it via scp too:"


[?] "Add Container Manifest Prodecure"
	In the next steps we will write in total 3 files (seat-application.json, mockservice.json, mock.py) inside 2 important directories: /data/var/containers/manifest/ and /data/var/mock .
	Before continuing with this guide, make sure to be in the right directory, run:
	```
	cd ~/../..
	```
	If you want, you can already create the mock directories that will constitute the python script to run mockservice:
	```
	mkdir ~/../../data/var/mock/
	touch ~/../../data/var/mock/mock.py
	```
	If you don't, you will be reminded later.


IMPORTANT PROBLEM: there seems to be a wrongful parsing of the lines where the above command to create a container parses each line like a separate command. Furthermore, the naming convention of 'seat-adjuster-app' should be clarified as being a placeholder name and should reflect instead the name of the template created in the very first steps.
	This means that either the parsing neets to be adjusted (create issue) or provide a different command formatting.
	The different command formatting looks like so:
		kanto-cm create --name seatadjuster-app --e="SDV_SEATSERVICE_ADDRESS=grpc://seatservice-example:50051" --e="SDV_MQTT_ADDRESS=mqtt://mosquitto:1883" --e="SDV_VEHICLEDATABROKER_ADDRESS=grpc://databroker:55555" --e="SDV_MIDDLEWARE_TYPE=native" --hosts="databroker:container_databroker-host, mosquitto:host_ip, seatservice-example:container_seatservice-example-host" ghcr.io/<YOUR_ORG>/<CONTAINER_IMAGE>:<tag>

		kanto-cm start --name seatadjuster-app
		kanto-cm logs --name seatadjuster-app
