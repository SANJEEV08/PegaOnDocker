# PegaOnDocker
This repository demonstrates how the PEGA Web Application along with its Database (PostgreSQL) can be deployed via containers with the help of Docker.

## Setup the PostgreSQL Database Container:

  1. Setup Docker 'secrets' to handle sensitive information:

       (a) Navigate and create a sub folder 'run' under the main working directory
       (b) Create an ENV file named 'secrets.env' with the following contents.

           POSTGRES_USER=postgres
           POSTGRES_PASSWORD=postgres

       (c) Iniate Docker Swarm to connect the working node to swarm.

           docker swarm init

       (c) Run the following command (applicable for a Windows Powershell terminal) to create Docker 'secrets' for the above two values.

              $secrets = Get-Content "E:\Sanjeev\Personal Projects\Projects - DevOps & Cloud\PegaOnDocker\secrets.env" | ForEach-Object {
              $name, $value = $_ -split '='
              [PSCustomObject]@{ Name = $name; Value = $value }
          }
          
          foreach ($secret in $secrets) {
              $secret.Value | docker secret create $secret.Name -
          }

       (d) Inspect any of the 'secrets'

           docker secret ls
           docker secret inspect POSTGRES_PASSWORD

           The output would look something like the below:

          [
                {
                    "ID": "pw948i5dl9n5svyi5jk14twsx",
                    "Version": {
                        "Index": 12
                    },
                    "CreatedAt": "2025-01-04T10:41:45.214136136Z",
                    "UpdatedAt": "2025-01-04T10:41:45.214136136Z",
                    "Spec": {
                        "Name": "POSTGRES_PASSWORD",
                        "Labels": {}
                    }
                }
            ]




  2. Write the Dockerfile - Create an Image - Initiate a Container:

      (a) Write the Dockerfile.

          # Base Image
          FROM pegasystems/postgres-pljava-openjdk:11
          EXPOSE 5432
          
          # Storing the location of the PostgreSQL Database files
          ENV PGDATALOC var/lib/postgresql-persist/data
          
          # Storing the value of the type of Database
          ENV POSTGRES_DATABASE postgres
          
          # Storing the Database username
          ENV POSTGRES_USER: /run/secrets.env/POSTGRES_USER
          
          # Storing the Database password
          ENV POSTGRES_PASSWORD: /run/secrets.env/POSTGRES_PASSWORD

     (b) Image Creation.

         docker build -t postgres -f Dockerfile-PostgreSQL .

     (c) Initiate a Container.

         docker run -p 5432:5432 --name postgres-container postgres

     (d) Try connecting to the Database via PGAdmin

         - Inspect the IPv4 address:

               docker inspect d0a74beb1d4e and check under "Networks"


     # References:

     - https://github.com/ColorsInTech/PegaInDocker/tree/master
     - https://github.com/kannan-raveendran-nair/pega-pe-docker/tree/master
     
      


     
     
