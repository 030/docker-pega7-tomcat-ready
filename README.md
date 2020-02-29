# Pega 7/8 Tomcat Docker container

This project produces a Docker image that you can use as a base image to create a complete Docker image for running Pega 8.  

This image is based on Tomcat 8.5.x which is based on OpenJDK's Java 8 image. 

Supported features:

* PR Web application container configuration (port 8080)
* Remote JMX management support (port 9001)

# Usability note

Although this code supports running Pega in a tomcat based docker container, it does not mean that Pega7 can be used in a docker container clustering environment as is, such as Docker Swarm and Kubernetes.
For that you have to extend this image.

# Using this image

The image itself is not runnable directly because it does not come with the Pega
 web applications.  Therefore you must use this image as a base to construct an 
 executable Docker image.

## Constructing your image

The simplest way to use this image is to create your own Dockerfile with contents similar to the example below and 
extract the Pega distribution to the same directory as the Dockerfile.  It is recommended that this is done on a Linux system to retain proper file permissions.  Replace the source paths with the actual paths to the Pega 7 software libraries and specify a valid JDBC driver for your target database.

    FROM nlmacamp/pega7-tomcat-ready:pega81
    
    # Expand prweb to target directory
    COPY archives/prweb.war /opt/pega/prweb.war
    RUN unzip -q -d /opt/pega/prweb /opt/pega/prweb.war
    
    # Make jdbc driver available to tomcat applications
    COPY /path/to/jdbcdriver.jar /usr/local/tomcat/lib/

Build the image using the following command:

    docker build -t pega:pega81 .

Upon successful completion of the above command, you will have a Docker
image that is registered in your local registry named pega7-tomcat:latest
 and that you can view using the `docker images` command.

## Running the image

The built image requires connectivity to a database that must be 
 available and seeded with the appropriate rule base via the Pega 7 standard installation
 utility.

    docker run -d -P --name pega -e DB_HOST=<host> ... pega:pega81

In the above command, the `-d`flag  tells Docker to run this program as a daemon process in 
 the background.  The `-P` flag tells Docker to expose all ports in the image to dynamically
 selected ports on the host machine.  The `-e` flag is a declaration of an environment
 variable.  See the [Dockerfile](Dockerfile) for the list of exposed variables.

# Accessing Pega

Use these instructions if you want to run Pega.

Once the image is running, you can connect to the Pega web application via the exposed bound
port.  To find this port (assuming you had them dynamically assigned as above), you can run 
the `docker ps` command to print out the port bindings.  Look for the 8080 port and connect to
it from your web browser at `http://host:port/prweb`.
