MongoDB for DeployDock - Docker images
========================================

This repository contains Dockerfiles for MongoDB images for DeployDock.
Users can choose between RHEL and CentOS based images.

Versions
---------------------------------
MongoDB versions currently provided are:
* mongodb-2.4
* mongodb-2.6

RHEL versions currently supported are:
* RHEL7

CentOS versions currently supported are:
* CentOS7


Installation
---------------------------------
Choose either the CentOS7 or RHEL7 based image:

*  **RHEL7 based image**

	To build a RHEL7 based image, you need to run Docker build on a properly
    subscribed RHEL machine.

	```
	$ git clone https://github.com/deploydock/mongodb.git
	$ cd mongodb
	$ make build TARGET=rhel7 VERSION=2.4
	```

*  **CentOS7 based image**

	This image is available on DockerHub. To download it run:

	```
	$ docker pull deploydock/mongodb-24-centos7
	```

	To build a MongoDB image from scratch run:

	```
	$ git clone https://github.com/deploydock/mongodb.git
	$ cd mongodb
	$ make build VERSION=2.4
	```

**Notice: By omitting the `VERSION` parameter, the build/test action will be performed
on all provided versions of MongoDB.**


Environment variables
---------------------------------

The image recognizes the following environment variables that you can set during
initialization by passing `-e VAR=VALUE` to the Docker run command.

|    Variable name          |    Description                              |
| :------------------------ | -----------------------------------------   |
|  `MONGODB_USER`           | User name for MONGODB account to be created |
|  `MONGODB_PASSWORD`       | Password for the user account               |
|  `MONGODB_DATABASE`       | Database name                               |
|  `MONGODB_ADMIN_PASSWORD` | Password for the admin user                 |


The following environment variables influence the MongoDB configuration file. They are all optional.

|    Variable name      |    Description                                                            |    Default
| :-------------------- | ------------------------------------------------------------------------- | ----------------
|  `MONGODB_NOPREALLOC` | Disable data file preallocation.                                          |  true
|  `MONGODB_SMALLFILES` | Set MongoDB to use a smaller default data file size.                      |  true
|  `MONGODB_QUIET`      | Runs MongoDB in a quiet mode that attempts to limit the amount of output. |  true


You can also set the following mount points by passing the `-v /host:/container` flag to Docker.

|  Volume mount point         | Description            |
| :-------------------------- | ---------------------- |
|  `/var/lib/mongodb/data`    | MongoDB data directory |

**Notice: When mouting a directory from the host into the container, ensure that the mounted
directory has the appropriate permissions and that the owner and group of the directory
matches the user UID or name which is running inside the container.**


Usage
---------------------------------

For this, we will assume that you are using the `deploydock/mongodb-24-centos7` image.
If you want to set only the mandatory environment variables and store the database
in the `/home/user/database` directory on the host filesystem, execute the following command:

```
$ docker run -d -e MONGODB_USER=<user> -e MONGODB_PASSWORD=<password> -e MONGODB_DATABASE=<database> -e MONGODB_ADMIN_PASSWORD=<admin_password> -v /home/user/database:/var/lib/mongodb/data deploydock/mongodb-24-centos7
```

If you are initializing the database and it's the first time you are using the
specified shared volume, the database will be created together with the database
administrator user and MongoDB admin user. After that the MongoDB daemon
will be started. If you are re-attaching the volume to another container, the
creation of the database user and admin user will be skipped and only the
MongoDB daemon will be started.


MongoDB admin user
---------------------------------

The admin user name is set to `admin` and you have to to specify the password by
setting the `MONGODB_ADMIN_PASSWORD` environment variable. This process is done
upon database initialization.


Changing passwords
------------------

Since passwords are part of the image configuration, the only supported method
to change passwords for the database user (`MONGODB_USER`) and admin user is by
changing the environment variables `MONGODB_PASSWORD` and
`MONGODB_ADMIN_PASSWORD`, respectively.

Changing database passwords directly in MongoDB will cause a mismatch between
the values stored in the variables and the actual passwords. Whenever a database
container starts it will reset the passwords to the values stored in the
environment variables.


Test
---------------------------------

This repository also provides a test framework which checks basic functionality
of the MongoDB image.

Users can choose between testing MongoDB based on a RHEL or CentOS image.

*  **RHEL based image**

    To test a RHEL7 based MongoDB image, you need to run the test on a properly
    subscribed RHEL machine.

    ```
    $ cd mongodb
    $ make test TARGET=rhel7 VERSION=2.4
    ```

*  **CentOS based image**

    ```
    $ cd mongodb
    $ make test VERSION=2.4
    ```

**Notice: By omitting the `VERSION` parameter, the build/test action will be performed
on all provided versions of MongoDB.**
