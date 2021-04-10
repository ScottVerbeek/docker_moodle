# docker_moodle

## Requirements

- Docker
- Docker-compose

## Usage

1. Clone this repository

```
git clone git@github.com:catalyst/docker_moodle.git docker_moodle
```

2. Clone Moodle code into siteroot

```
cd docker_moodle
git clone git@github.com:moodle/moodle.git siteroot
```

3. Copy site config across

```
cp moodle-config siteroot/config.php
```

4. Start containers

```
docker-compose up
```

## Utility Commands

Use the following command to enter the bash shell of each container.
Replaces using the docker exec function.

To change container names, change name in yaml file and control file.

Enter web container:

```
./control web
```

Enter db container:

```
./control db
```

Enter test database container:

```
./control testdb
```

## Running Tests

To setup the testing environment run:

```
./control web
composer install
php admin/tool/phpunit/cli/init.php
```

To run tests:

```
./control web
./vendor/bin/phpunit
```

## Setup Xdebug for VS Code

Open vscode and click `create a launch.json file` then click PHP. VS Code will create a JSON file for you edit it to the following:
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/var/www/html": "${workspaceFolder}"
            }
        },
        {
            "name": "Launch currently open script",
            "type": "php",
            "request": "launch",
            "program": "${file}",
            "cwd": "${fileDirname}",
            "port": 9003
        }
    ]
}
```
