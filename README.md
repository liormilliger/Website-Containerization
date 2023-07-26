# Website-Containerization
This project uses AWS EC2 instance on which we will run our Docker containe

## Work Flow

#### Step 1 - Launching EC2 instance with user data
#### Step 2 - Creating artifact for docker image
#### step 3 - Create an image and run a container
#### Step 4 - Validation
#### Step 5 - Cleanup


## STEP 1 - LAUNCHING EC2 instance

* login to your AWS account and Launch an EC2 instance:
*  Name: Docker-Engine
*  OS: Ubuntu Server 22.04 LTS (Free Tier)
*  Type: t2.micro
*  Key pair: docker-key.pem
*  Security Group: (Create - Edit - Inbound rules) 22/MyIP alltraffic/MyIP
*  Advanced Details - scroll to user data
*  Copy paste userdata file content
*  Launch instance
> In the user data we are using 2 packages: apt-get and apt.
> 
> First we update them and then we install the packages and dependencies.
>
> The last command makes ubuntu user part of docker group
>
> for authorizing operations from ubuntu user on docker


## STEP 2 - CREATING ARTIFACT FOR DOCKER IMAGE

> In this step we will download a zip file of the website, open and archive it as a tar.gz
> 
> and move it to our working directory (zip file will not be extracted in the build process,
>
> it has to be a different archive file - we will use tar).


* open a browser with www.tooplate.com and with developer tools (split window)
> make sure you are on “Network” tab
* choose a template (I chose mini finance website) and click “Download”
> On the Developer tool screen you will see a zip file
* mark it and get the request URL (copy it)
> for me its https://www.tooplate.com/zip-templates/2135_mini_finance.zip
* Open your terminal and SSH to your Docker-Engine EC2 via its public IP
* (SSH -i <path/to/key.pem> ubuntu@<Public IP>)
> We will open a working directory, get the file (wget) through the link,
>
> open it, archive it with tar and clean up:

#### Follow these commands
* sudo apt unzip -y → Downloading the unzip package
* mkdir images → creating a directory for images
* cd images
* mkdir mini → creating working directory
* wget https://www.tooplate.com/zip-templates/2135_mini_finance.zip → downloading the zip file
* unzip 2135_mini_finance.zip → unzipping it
* cd 2135_mini_finance 
* tar czvf mini.tar.gz * → archiving it with tar as gzip to create the artifact
* mv mini.tar.gz ../ → moving the artifact from this folder to the parent folder
* cd .. 
* rm -rf 2135_mini_finance.zip 2135_mini_finance → Deleting residuals
* mv mini.tar.gz mini/ → moving the artifact to working directory
* cd mini

## STEP 3 - Create an image and run a container
> In this step we will create a Dockerfile using our artifact
>
> to create an image from which we will run our container
>
> and the container will carry our website
>
> There are some good documentation regarding how to build a Docker file:
>
> https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
>
> https://docs.docker.com/engine/reference/builder/

#### Creating Dockerfile (Copy-Paste the content of Dockerfile in here)
#### MAKE SURE to change the Dockerfile to fit your choices:
#### Most important (line 11) ADD should have YOUR artifact name
* vim Dockerfile
* paste content
* :wq → save and exit
> Run the following commands:
* docker build -t miniimg . → Building the image
* docker images → to see all images
> Now we can run the container from the image, with port mapping to 9080:
* docker run -d –name miniwebsite -p 9080:80 miniimg
* docker ps → to see the process running

## STEP 4 - VALIDATION
> go to the browser and in the url line put the public IP of the Docker-Engine Ec2 instance
>
> and the port like this: <public IP>:9080
>
> The website should upload.
## We containerized our website!

## STEP 5 - CLEAN-UP
* Exit (terminate) the website browser
> back to the terminal:
* docker stop miniwebsite → stops the container
* docker rm miniwebsite → deletes the container
* docker rmi miniimg → deletes the image
* Go to AWS EC2 instance page and terminate the instance

That's it!
