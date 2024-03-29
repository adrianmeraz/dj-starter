# DJ Starter - Bootstrap Django Projects

[![codecov](https://codecov.io/gh/adrianmeraz/dj-starter/branch/master/graph/badge.svg?token=SV085I4DGJ)](https://codecov.io/gh/adrianmeraz/dj-starter)
![test-status](https://github.com/adrianmeraz/dj-starter/actions/workflows/dev.yml/badge.svg?branch=dev)

## Setup Local Dev Environment

### Setup SSH Keys

#### Generate SSH Keys

Run command:

`ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"`

Hit enter to accept default file location, and again to accept empty passphrase

#### Copy Public Key

Install xclip

`sudo apt-get install xclip`

To copy to clipboard, run:

`xclip -sel clip < ~/.ssh/id_rsa.pub`

### Install IntelliJ Toolbox

Download tarball from [HERE](https://www.jetbrains.com/toolbox-app/)

Extract tarball and replace `<VERSION>` with the actual package name:

`sudo tar -xzf jetbrains-toolbox-<VERSION>.tar.gz -C /opt`

Run the install script. Replace `<VERSION>` with the actual package name:

```
cd /opt/jetbrains-toolbox-<VERSION>
./jetbrains-toolbox
```

Follow prompts and install Pycharm Community IDE

### Install Curl

`sudo snap install curl`

### Install python-venv

`sudo apt install python3.9-venv`

### Install distutils

`sudo apt install python3-distutils `

### Install Poetry

```
sudo curl -sSL https://install.python-poetry.org | python3 -
```

### Add Poetry Env Variable

Append the following to `~/.profile`

`export PATH="/home/adrian/.local/bin:$PATH`

Test the poetry install:

`poetry --version`

### Pull Dev Repo

Create project directory and initialize Git

`git init`

Add Remote Origin

`git remote add origin git@github.com:adrianmeraz/dj-starter.git`

Pull repo

`git pull`

Create new branch and switch to branch

`git checkout -b <BRANCH_NAME>`

Delete local branch

`git branch -d <BRANCH_NAME>`

### Add .env file

Use .env example to set environment variables.

**NOTE**

When deploying to remote environments, the environment variables must be set in Git secrets, as well as
the lambda deployment.

### Verify Github Action Secret

`DATABASE_URL` should be set to `postgres://postgres:postgres@localhost:5432/postgres`

This should match values in the github actions yml

### Install Poetry

Install poetry with instructions [HERE](https://python-poetry.org/docs/#installation)

Verify the installation with a version check

`poetry --version`

If command not found, set the path variable:

`source $HOME/.poetry/env`

### Install Dependencies

`poetry install`

### Generate the secret key
```
import secrets

print(secrets.token_urlsafe())
```

Add to .env file as _SECRET_KEY_ environment variable

## Local Database Setup

### Setup Postgres Local Instance (Ubuntu)

```
sudo apt update
sudo apt install postgresql postgresql-client
```

### Check Instance Status

```
systemctl status postgresql.service
```

### Change System User Password

**NOTE: Don't use the @ symbol in the password!**

```
sudo su - postgres
psql -c "alter user postgres with password '<DB_PASSWORD>'"
```

### Start PSQL Shell

`psql`

### Get Connection Details

`\conninfo`

Details should look similar to this:
```
You are connected to database "postgres" as user "postgres" via socket in "/var/run/postgresql" at port "5432".
```

### Create Local Postgres Database

Replace placeholder values with real values

```
sudo -u postgres psql
postgres=# create database <DB_NAME>;
postgres=# create user <DB_USERNAME> with encrypted password '<DB_PASSWORD>';
postgres=# grant all privileges on database <DB_NAME> to <DB_USERNAME>;
postgres=# ALTER USER <DB_USERNAME> CREATEDB;
```

Example:

```
create database djstarter;
create user djstarter with encrypted password 'djstarter';
grant all privileges on database djstarter to djstarter;
ALTER USER djstarter CREATEDB;
```

To Delete the local database, run:

```
postgres=# DROP DATABASE <DB_NAME>;
```

Example:

```
postgres=# DROP DATABASE djstarter
```

### Local jdbc string

`postgres://<DB_USERNAME>:<DB_PASSWORD>@localhost:5432/<DB_NAME>`

### Create Database on Existing Instance using admin command (Optional)

The initial database url <DB_INSTANCE_URL> will be:

`postgres://postgres:<PASSWORD>@localhost:5432/postgres`

Run the command from the project root to create the database and service user

`poetry run python manage.py create_db --db-url="<DB_INSTANCE_URL>" --db-name="<NEW_DB_NAME>" --db-username="<NEW_DB_USERNAME>" --db-password="<NEW_DB_PASSWORD>"`

### Initialize Database

```
poetry run python manage.py makemigrations
git add .
poetry run python manage.py migrate
poetry run python manage.py createsuperuser
```

### Create Super User

Set the following environment variables:

DJANGO_SUPERUSER_EMAIL
DJANGO_SUPERUSER_USERNAME
DJANGO_SUPERUSER_PASSWORD

```
poetry run python manage.py makemigrations
git add .
poetry run zappa manage <STAGE_NAME> "migrate"
poetry run zappa manage <STAGE_NAME> "createsuperuser --noinput"
```

## Admin Commands

## Poetry Utilities

### Create Initial Poetry File
```
poetry init
```

### Install All Requirements
```
poetry install
```

### Update all Poetry Packages
```
poetry update
```

### Update Individual Poetry Package
`poetry update <PACKAGE_NAME>`

### View All Requirements
`poetry show --tree`

### List Virtual Envs
`poetry env list`

### Get Poetry Env Info
`poetry env info`

### Remove Virtual Env
`poetry env remove <ENV_NAME>`

### Clear Cache
`poetry cache clear . --all`

### Poetry Executable Path
`which poetry`

## Database Utilities

### Migrations

After any Model Updates:
1. Create migrations
2. Add migrations to git
3. Migrate to run DB changes

```
poetry run python manage.py makemigrations
git add .
poetry run python manage.py migrate
poetry run python manage.py createsuperuser
```

## Testing

Specify settings with `--settings`

### Running tests

`poetry run python manage.py test --settings=config.settings.local --no-input --parallel`

To run against a single module, add the module name:

`poetry run python manage.py test djstarter.tests.test_views --settings=config.settings.local --no-input --parallel`

### Faster Tests

**NOTE** Tests must be isolated and able to run independently! The `--parallel` flag causes incorrect values

```
coverage run manage.py test --settings=config.settings.local --keepdb
```

### Generate Coverage Report

Must be run after coverage tests have been run

```
coverage report
```

### Generate Coverage HTML Report

```
coverage html
```

This creates the directory coverage html. Open the index.html to see the full report

### Coverage Issues

If the numbers reported aren't what's expected, or tests are missing, verify that an empty `__init__.py`
is in the `tests` directory for each app.

---

## Contributions

Contributions are encouraged and welcome! 

The general steps to contribute are:

1. Create an issue for a feature/bug, if not already created
2. Create a feature branch with the issue in the name
   1. Example: If the issue is `#123`, create the branch as `feature/DC-123`
3. Write Tests
4. Write Code - All code must have tests and cannot lower coverage!
5. Verify all tests pass
6. Pull all and merge all changes from the dev branch
7. Create a pull request to merge into the dev branch