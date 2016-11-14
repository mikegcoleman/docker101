# Build Ship Run

## Demo setup

Make sure these things are done before you run this demo for the very first time. 

1. Clone this repo onto the demo laptop

	`git clone https://github.com/mikegcoleman/docker101.git`

1. Make sure Docker for Mac or Docker for Windows is installed. 

1. You will need a second Docker Host. The best example is to use a cloud-based instance. But, you can use a local VM. In either case, you'll need to be able to SSH into the 2nd machine, and you'll need its IP address. Setting up this second machine is outside the scope of this script. 

1. Make sure you have a Docker Hub account. You can sign up for free at `http://hub.docker.com`

1. On your local machine make sure you log into docker hub by using the `docker login` command

1. Build the catweb image and push it to docker hub. These instructions assume you cloned the repo in step 1 to your home directory, if not adust the file path accordingly. 

	```
	cd ~/docker101/catweb
	docker build -t <YOUR DOCKER HUB ID>/catweb:latest .
	docker push <YOUR DOCKER HUB ID>/catweb:latest
	```
	**Note**: If you get an error saying you need to authenticate, you'll need to log in to Docker Hub. See the previous step
	
## Running the demo

This demo is designed to show a build, ship, run workflow using a simple Flask-based web app, catweb. 

The basic flow is run the app run the app locally, modify the web template to show how hot mounting a volume works, build an updated image, push to Docker Hub, then deploy on your second Docker host.

If you've not run the demo before, it is advised to watch the video in the repo to see how to talk through each step. 

1. `cd ~/docker101/catweb` (this location may vary depending on where the repo was cloned to)

1. Show and explain the Dockerfile and how the app is built, you can also show the source code if you wish. The Dockerfile is the same one used in the slides. 

1. Run the app and mount the local directory into the source code directory in the container

	`docker run -d -p 5000:5000 --name catweb -v $(pwd):/usr/src/app <YOUR DOCKER HUB ID>/catweb:latest`
	
1. Switch into a browser on your local machine and show the app running at `http://localhost:5000`

1. Switch back to your terminal and edit the `index.html` file in the `templates` directory. Usually I change it by using an attendee's name in the title e.g. "Josephine's Random Cat Gif". Save your changes. 

	**Note**: If you `cd` into the `templates` directory to edit the file, make sure to `cd` back into the `catweb` directory before rebuilding the image in a few steps
	
1. Switch back to the web browser and show how the change you made is immediately reflected in the running container. 

1. Switch back to the terminal and build a new version of the catweb image.

	**Note**: I usually tag the image w/ the name I used when I edited the `index.html` file so it's easier to remember which one to deploy
	
	`docker build -t <YOUR DOCKER HUB ID>/catweb:<TAG> .`
	
1. Push the new image to DTR. 

	`docker push <YOUR DOCKER HUB ID>/catweb:<TAG>`
	
	**Note**: If you get an error saying you need to authenticate, you'll need to log in to the DTR server. See the setup instructions above. 
	

1. Explain you're now going to simulate deploying an app to "production" by running catweb on a second machine. 

	SSH into your SECOND machine
	
1. Run catweb on the SECOND machine:

		docker run -d -p 5000:5000 --name catweb <YOUR DOCKER HUB ID>/catweb:<TAG>

1. Show catweb running on the 2nd machine by browsing to port 5000 at the IP of the 2nd machine. For instance `http://192.168.0.1:5000

## Post Demo Clenup
**NOTE** Do this after EACH demo

1.  Stop and remove the running catweb container on BOTH machines:

		docker stop catweb
		docker rm catweb
		
1. You might also want to remove the version of the image you built in step 7. Again, run the following command on both machines.

		docker rmi <YOUR DOCKER HUB ID>/catweb:<TAG>


	



