# WordPress Environment with Docker Compose

This project sets up a local WordPress environment using Docker Compose. It includes the following services:
- **MariaDB Database**
- **PHPMyAdmin Interface**
- **WordPress**

## Prerequisites
- [Docker](https://www.docker.com/) installed on your system.
- [Docker Compose](https://docs.docker.com/compose/) installed.

## Getting Started
1. Clone this repository:
    ```sh
    git clone 
    cd wordpress-docker
    ```

2. Create a .env file with the following variables:
    ```sh
    MYSQL_ROOT_PASSWORD=your_root_password
    MYSQL_DATABASE=your_database_name
    MYSQL_USER=your_database_user
    MYSQL_PASSWORD=your_database_password
    ```
    
4. Start the Docker containers:
    ```sh
    docker-compose up -d
    ```

5. Access the services:
    WordPress:  http://localhost:8080
    PHPMyAdmin: http://localhost:8081

## Stopping the Services
    To stop the services, run:
    ```bash
    docker-compose down
    ```

## Persistent Data
Database data is stored in the db Docker volume.

## Customization
Modify the WordPress files by editing the contents of the ./ directory.

## Additional Notes
Ensure that port 8080 (for WordPress) and 8081 (for PHPMyAdmin) are not in use by other applications.

You can configure memory allocation and other parameters in the docker-compose.yml file.
