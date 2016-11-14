# Build Ship Run

## Demo setup

1. If you haven't already clone this repo onto the demo laptop

	`git clone https://github.com/ManoMarks/vmworld2016demo.git`

1. Make sure Docker for Mac is installed. 

1. Edit the `/etc/hosts` file on the demo laptop and add the following entry for `www.catweb.demo` (it points to the IP of the load balancer for the UCP nodes)

	`13.91.251.79    www.catweb.demo`
	
2. In the advanced preferences for Docker for Mac add `dtrdemo.westus.cloudapp.azure.com` under `Insecure registries` and click `Apply & Restart`

## Running the demo

This demo is designed to show a build, ship, run workflow using a simple Flask-based web app, catweb. 

The basic flow is run the app run the app locally, modify the web template to show how hot mounting a volume works, build an updated image, push to DTR, then deploy on Azure using Docker Data Center.

1. `$ cd ~/vmworld2016demo/catweb` (this location will vary depending on where the repo was cloned to)

1. Show and explain the Dockerfile and how the app is built, you can also show the source code if you wish. 

1. Run the app and mount the local directory into the source code directory in the container

	`$ docker run -d -p 5000:5000 --name catweb -v $(pwd):/usr/src/app dtrdemo.westus.cloudapp.azure.com/demouser/catweb`
	
1. Switch into the browser and show the app running at `http://localhost:5000`

1. Switch back to your terminal and edit the `index.html` file in the `templates` directory. Usually I change it by using an attendee's name in the title e.g. "Josephine's Random Cat Gif". Save your changes. 

	**Note**: If you `cd` into the `templates` directory to edit the file, make sure to `cd` back into the `catweb` directory before rebuilding the image in a few steps
	
1. Switch back to the web browser and show how the change you made is immediately reflected in the running container. 

1. Switch back to the terminal and build a new version of the catweb image.

	**Note**: I usually tag the image w/ the name I used when I edited the `index.html` file so it's easier to remember which one to deploy
	
	`$ docker build -t dtrdemo.westus.cloudapp.azure.com/demouser/catweb:<tag>`
	
1. Push the new image to DTR. 

	`$ docker push dtrdemo.westus.cloudapp.azure.com/demouser/catweb:<tag>`
	
	**Note**: If you get an error saying you need to authenticate, you'll need to log in to the DTR server
	
	`$ docker login -u demouser -p demo dtrdemo.westus.cloudapp.azure.com`

1. In a web browser navigate to `https://dtrdemo.westus.cloudapp.azure.com`. If prompted to log in the username is `demouser` and the password is `demo`.

	Click on `Repositories` and show the image you just uploaded
	
1. In a web browser navigate to `https://ucpdemo.westus.cloudapp.azure.com` and log into UCP. Feel free to give a quick tour of UCP and some of its features. 

1. From the left menu, click `Containers`

	**Note**: Sometimes the left menu is blank, simply reload the page if this happens
	
1. Click the `+ Deploy Container` button and fill in the following values

	1. Image name: `dtrdemo.westus.cloudapp.azure.com/demouser/catweb:<tag>`
	2. Under `Network` make sure the box next to `Automatically expose all ports` is checked
	3. Under `Labels` add two lables:
		`interlock.hostname` and `www`
		`interlock.domain` and 	`catweb.demo`
		
		**Note**: Make sure to click the plus after each one
		
1. Click `Run Container`

1. After the container is successfully deployed, navigate in the web browser to `http://www.catweb.demo` This will navigate to your newly deployed container running on Azure. 

## Post Demo Clenup
**NOTE** Do this after EACH demo

1. In Universal Control Plane remove the container you just deployed. If you don't there will be conflicting versions running. 


	



