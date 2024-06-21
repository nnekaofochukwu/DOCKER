

code
docker build -t my-app .

Here is a breakdown of the command:

docker build is the command to build a Docker image.

-t my-app tags the image with the name my-app. You can replace my-node-app with any name you prefer.

. specifies the build context, which is the current directory (.).



Running the Container
Once you have built your Docker image and ensured your application is set to listen on port 3000, you can run the container using:

Copy code
docker run -p 3000:3000 my-app

Additional Considerations
Detached Mode (-d): If you want to run the container in detached mode (in the background), you can add the -d flag:

Copy code
docker run -d -p 3000:3000 my-app

List Running Containers
To list all running containers:

Copy code
docker ps
This command will display a list of currently running Docker containers along with details such as container ID, 
image used, command running in the container, creation time, status, and ports mapped.

List All Containers-To list all containers, including those that are stopped:

Copy code
docker ps -a

The -a flag (or --all) shows all containers, both running and stopped. 

This is useful for viewing the status and details of containers that have been previously 
created but are not currently running

Start a Container
To start a stopped container, use the docker start command followed by the container ID or name:

Copy code
docker start <container_id_or_name>

For example, if your container ID is a1b2c3d4e5f6, you would start it with:

Copy code
docker start a1b2c3d4e5f6

Stop a Container
To stop a running container, use the docker stop command followed by the container ID or name:

Copy code
docker stop <container_id_or_name>

For example, to stop a container with ID a1b2c3d4e5f6, you would use:

Copy code
docker stop a1b2c3d4e5f6

Remove a Container
To remove a container (which must be stopped), use the docker rm command followed by the container ID or name:

Copy code
docker rm <container_id_or_name>

For example, to remove a container with ID a1b2c3d4e5f6, you would use:

Copy code
docker rm a1b2c3d4e5f6

Additional Options and Tips
Force Removal: If the container is running and you want to forcefully remove it, 
you can add the -f flag to the docker rm command:

Copy code
docker rm -f <container_id_or_name>

Remove Stopped Containers: To remove all stopped containers, you can use the docker container prune command:

Copy code
docker container prune
This command will prompt you to confirm before removing all stopped containers.

Multiple Containers: You can specify multiple container IDs or names separated by spaces to perform the same 
operation on multiple containers at once:

Copy code
docker stop container1 container2
docker rm container1 container2
These commands provide essential functionality for managing Docker containers, allowing you to start, stop, 
and remove containers as needed during development, testing, or production operations. 
Adjust commands based on your specific container IDs or names.
