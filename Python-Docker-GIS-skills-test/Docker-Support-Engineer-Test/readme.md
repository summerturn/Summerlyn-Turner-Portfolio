# Walkthrough

## Deliverables

 1. list of commands I used to complete this process
    - Commands used:
        1. `docker --version`
        2. `docker inspect docker-support-engineer-test_database_1`
        3. `docker-compose up`
        4. `docker volume inspect gis_database-data`
        5. `docker container stop b9fda763f83e`
        6. `docker container rm --volumes b9fda763f83e`
        7. `docker image rm b9fda763f83e`
        8. `docker ps`
        9. `docker run --name b36e5bfba72b -e POSTGRES_PASSWORD=password -d -p 5432:5432 postgres`
        10. `docker exec -it <container name> /bin/bash`
        11. `psql postgresql://postgres:5432@localhost:5432/postgres`
        12. `postgres --version`
        13. `SELECT * FROM pg_settings WHERE name = 'port';`


 2. yml file for postgres configuration (and env file)

    1. [Link to my yml](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker-compose.yml)

    2. [Link to my env](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/database.env)

## Goal

1. Install dependencies (depending on your environment you'll need to install more libraries)

    - Because I am doing this work on a Mac I downloaded Docker Desktop to get my install for Docker and Docker Compose.

2. Check Docker & Docker Compose version.
    
    - Using `docker --version` in terminal returns the current Docker version which is "Docker version 20.10.6, build 370c289"

    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/docker_version_check.png)

    - The Docker Compose version can be found if you run `docker-compose version`

    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/docker_compose_version.png)

3. Start a postgres instance using docker-compose
    
    - To create my postgres instance, I downloaded the [Dockerfile](https://github.com/bitnami/bitnami-docker-postgresql/blob/12.7.0-debian-10-r18/12/debian-10/Dockerfile) of the desired version from the docker-postgres github.

    - After that, I create a [yml](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker-compose.yml) file and an [env](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/database.env) file to define the parameters of the postgres instance.

    - Within the yml, I define the version, the database image name, the postgres login credentials located in the env file that I created, and the volume.

    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/yml_version.png)

    - Once I have all the required files created, I run `docker-compose up` to create the instance

    - Now the instance is up and running

    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/postgres_terminal1.png)
    
    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/postgres_terminal2.png)



4. Check docker volume location

    - I use `docker volume inspect <container_name>` to inspect the volume and to see its location or "Mountpoint" and other information

     ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/docker_volume_check.png)

5. Stop the instance

    - To check the instance is still running, run `docker ps`. If the container is no longer on the list it is inactive or deleted!

    - To stop the instance, I run `docker container stop <container_name>`. This command stops the specific instance name passed.

    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/docker_stop.png)


    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/postgres_shutdown_msg.png)
   

6. Destroy any trace of Docker instances 🔥

    - To destroy any trace of my Docker instance, I run `docker container rm --volumes <container_name>`. The addition of `--volumes` ensures that the volumes associated with this instance are also removed.

    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/docker_remove_instance.png)

    - To remove the image of the instance, run `docker image rm <container_name>`


## Bonus

1. Enter postgres container

    - First, I run the container itself with `docker run --name <postgres_image_name> -e POSTGRES_PASSWORD=password -d -p 5432:5432 postgres`
    - To enter the postgres container, I run `docker exec -it <container name> /bin/bash`

2. Check postgres port

    - To access to postgres uri I can run `psql postgresql://postgres:1234@localhost:5432/postgres` and can run sql commands here.

    - Once inside the uri I can check the postgres port number with sql by querying `SELECT * FROM pg_settings WHERE name = 'port';`

    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/bonus_postgres_port_num.png)

    - Or you can just run `docker ps`

3. Check postgresql version

    - To check the postgreSQL verion I run `postgres --version`

    ![image](https://github.com/summert21/CARTO-skills-test/blob/master/Docker-Support-Engineer-Test/docker_setup_screenshots/bonus_postgresql_version.png)




