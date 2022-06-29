- In order to properly use official images, you will have to read the image's official documentation on Docker Hub to know how to use them

- We can target specific services when running `docker-compose up`
  - Example
    - `docker-compose up -d [service-a] [service-b] [service-c]`

- You can set certain settings that you would normally set in a Dockerfile to essentially overwrite or add the settings inside of the Dockerfile
  - If you're using the same Dockerfile for multiple services, and want to add settings to it that would be used in one service but not the other, there's usually a key to add in the docker compose file that will fulfill that for you.
  - Example: say that I'm using the same Dockerfile for two services, service-a and service-b
    - service-a:  
        build:  
          context: ./dockerfiles  
          dockerfile: Dockerfile.php  
      service-b:  
        build:  
          context: ./dockerfiles  
          dockerfile: Dockerfile.php  
        entrypoint: ["php", "/var/www/html/artisan"]  
    - You would add the additional setting the same way you would add it in the Dockerfile
    - Here, we're adding an entrypoint setting to **service-b**, which gives us the flexibility to a) not create a new Dockerfile, and b) to only include this setting in this particular service

- In the Laravel application code, specifically the .env file, you will need to change the following entries if using multiple containers
  - DB_HOST  
    DB_DATABASE  
    DB_USERNAME  
    DB_PASSWORD  
  - Make sure these values are set to the values you defined as your environment variables (or what you defined in the file listed in your `env_file` key in the docker-compose file) for the mysql container before you do the database migrations in your artisan service (specific to a PHP/Laravel project) (`docker-compose run --rm artisan migrate`)
