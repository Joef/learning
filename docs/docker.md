Creating a new image for docker hub:
```bash
docker build -f dicom-listener/Dockerfile -t imagemovermd/dicom-listener:TAG .
```
In the above example:

`-f` file
`-t` tag

Pushing the build image to the hub:

    docker push imagemovermd/dicom-listener:TAG

Connecting to local docker

    docker exec -it <container-name> bash


Starting docker from a compose file

    docker-compose up -d 

Or bring it down

    docker-compose down

On Linux:

sudo systemctl restart docker

## For the Postgres container work

Connect, and then:

    psql postgres postgres

Show help

    \?
    \h

Show tables

    \z

### Basic Queries

Do not omit the `;` and Table names should be in quotes:

    SELECT * FROM "Users";

[Reference](https://chartio.com/resources/tutorials/how-to-list-databases-and-tables-in-postgresql-using-psql/)

### Sequelize

    CREATE DATABASE patients;
    cd server
    sequelize-cli migration:generate --name create-patient-visits 
    sequelize-cli db:migrate



## For Pronghorn

`cd` to the root

Make sure that the correct grails and java version are being used. i.e.

    jdk 1.7.0_80

Build the container

    docker build -f Dockerfile -t imagemovermd/pronghorn:WITS-2856 .

Then, push the container

    docker push imagemovermd/pronghorn:WITS-2856

Note, these images are quite large. 306MB compressed.

## Release process

    git checkout v4.0.1
    git pull
    npm i
    npm run release v4.0.1-4
    git push --follow-tags origin v4.0.1
    docker build . -t imagemovermd/pronghorn:rc4.0.1-4
    docker push imagemovermd/pronghorn:rc4.0.1-4

Where `v4.0.1-4` is the tag