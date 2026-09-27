# Local XAMPP-Like Environment

A containerized development environment that imitates:

- PHP 7.4.1
- Apache
- MariaDB 10.4.11
- phpMyAdmin 5.0.1

## Requirements

- Podman
- podman-compose

## Start the Environment

For the first build:

```powershell
podman-compose up -d --build
```

For normal startup:

```powershell
podman-compose up -d
```

Check the container status:

```powershell
podman-compose ps
```

## Access the Services

- Application: http://localhost:8081
- phpMyAdmin: http://localhost:8080

phpMyAdmin connection details:

```text
Server: db
Username: root
Password: rootpassword
Database: app_db
```

## Backup Images

Build the Apache/PHP image:

```powershell
podman-compose build web
```

Save all required images:

```powershell
podman save -o trial-images.tar trial-web:latest mariadb:10.4.11 phpmyadmin/phpmyadmin:5.0.1
```

The image archive does not contain:

- MariaDB database data
- The `src` folder
- The MariaDB volume

## Reuse the Image Backup

Load the saved images:

```powershell
podman load -i trial-images.tar
```

Start the environment without rebuilding:

```powershell
podman-compose up -d
```

Do not use `--build` when reusing the backup. It may try to download packages from the old Debian mirror.

## Reset the Database

Warning: this permanently deletes all MariaDB data.

```powershell
podman-compose down -v
podman-compose up -d
```

This creates a new blank database using the credentials in `docker-compose.yml`.

## Stop the Environment

Stop and remove the containers:

```powershell
podman-compose down
```

To also delete the database volume:

```powershell
podman-compose down -v
```
