<h1 align="center" style="border-bottom: none;">:ballot_box_with_check::crossed_swords:&nbsp;&nbsp; Todoist ⇒ Habitica Sync &nbsp;&nbsp;:crossed_swords::ballot_box_with_check:</h1>
<h3 align="center">One way synchronisation from Todoist to Habitica</h3>

[![Sync to Habitica](https://github.com/junefish/todoist-habitica-sync/actions/workflows/run-command.yml/badge.svg?branch=main)](https://github.com/junefish/todoist-habitica-sync/actions/workflows/run-command.yml)

Because if you want to be productive, you should not track tasks in two places. Habitica is great for tracking habits, Todoist for tracking TODOs. Now you can get the best of both.

## How it works

Tasks are not added immediately. Only when you finish a task in Todoist, new task will be created in Habitica, finished and immediately deleted. So you won't see any Todoist tasks in Habitica but still get the rewards.

# Features

* Scores points in Habitica when Todoist task is finished.
* Works for repeated tasks in Todoist, as long as the date string contains `every`.
* To form a positive habit of prioritizing long term tasks, difficulty in Habitica is assigned by priority in Todoist. The priority is them assumed to match [Eisenhower Matrix](https://en.wikipedia.org/wiki/Time_management#The_Eisenhower_Method) quadrants.

| Todoist Priority | Habitica Difficulty |    Eisenhower Quadrant    |
|:----------------:|:-------------------:|:-------------------------:|
|        p1        |        Medium       |     Important, urgent     |
|        p2        |         Hard        | Important, not urgent     |
|        p3        |         Easy        |   Not important, urgent   |
|        p4        |       Trivial       | Not important, not urgent |

# Installation

## As a script

1. Open terminal
2. Make sure [`git`](https://github.com/git-guides/install-git) is installed:
   ```shell script
   git --version   
   ```
   The output should look something like:
   ```text
   git version 2.25.1
   ```
3. Make sure Python 3.10+ is installed:
   ```shell script
   python --version
   ```
   The output should look something like:
   ```text
   Python 3.10.1
   ```
   * If it shows `2.7.x` instead, try `python3` instead and use it in the rest of the guide.
   * If it shows `3.9.x` or lower, use [`pyenv`](https://github.com/pyenv/pyenv#installation) to install a higher version of Python on your system.
4. Make sure [`poetry`](https://python-poetry.org/docs/#installation) is installed:
   ```shell script
   poetry --version
   ```
   The output should look something like:
   ```
   Poetry version 1.1.5
5. Clone this repository:
   ```shell script
   git clone https://github.com/radeklat/todoist-habitica-sync.git
   cd todoist-habitica-sync
   ```
6. Copy the `.env.template` file into `.env` file:
   ```shell script
   cp .env.template .env
   ```
7. Edit the `.env` file, fill all missing values and/or change existing ones to suit your needs.
8. Install all application dependencies:
   ```shell script
   poetry install --only main
   ```
9. Run the app:
    ```shell script
    poetry run python src/main.py
    ```

## As a docker container

1. Open terminal
2. Make sure [`docker`](https://docs.docker.com/get-docker/) is installed:
   ```shell script
   docker --version   
   ```
   The output should look something like:
   ```text
   Docker version 20.10.5, build 55c4c88
   ```
3. Create a base folder structure for the service:
   ```shell script
   mkdir -p todoist-habitica-sync/.sync_cache
   cd todoist-habitica-sync
   chmod 0777 .sync_cache
   # Download .env file template
   curl https://raw.githubusercontent.com/radeklat/todoist-habitica-sync/master/.env.template --output .env
   ```
4. Edit the `.env` file and fill the missing details.
5. Run the container:
   ```shell script
   docker run \
      --pull always --rm --name todoist-habitica-sync \
      --env-file=.env \
      -v $(pwd)/.sync_cache:/usr/src/app/.sync_cache \
      radeklat/todoist-habitica-sync:latest
   ```
   This configuration will run the service in the foreground (you need to keep the terminal open) and always use the latest version.
   * Change `latest` to [a specific version](https://hub.docker.com/repository/registry-1.docker.io/radeklat/todoist-habitica-sync/tags) if you don't always want the latest version or remove the `--pull always` flag to not update.


Add `--detach` flag to run in the background. You can close the terminal, but it will not start on system start up.
* To see the log of the service running in the background, run `docker logs todoist-habitica-sync`
* To stop the service, run `docker container stop todoist-habitica-sync`

## As a service

You can use the above mentioned docker image to run the sync as a service on server or even your local machine. The simples way is to use docker compose:

1. Make sure you have [`docker`](https://docs.docker.com/get-docker/) installed:
   ```shell script
   docker --version
   docker compose version
   ```
   The output should look something like:
   ```text
   Docker version 23.0.2, build 569dd73
   Docker Compose version v2.17.2
   ```
2. Download the example compose file:
   ```shell script
   mkdir -p todoist-habitica-sync/.sync_cache
   cd todoist-habitica-sync
   chmod 0777 .sync_cache
   ```
3. Download a compose file and an example .env file:
   ```shell script
   BASE_URL=https://raw.githubusercontent.com/radeklat/todoist-habitica-sync/master
   curl $BASE_URL/.env.template --output .env
   curl $BASE_URL/docker-compose.yml -O
   ```
4. Edit the `.env` file and fill the missing details.
5. Run the service:
   ```shell script
   docker compose up
   ```
   This command will run the service in the foreground (you need to keep the terminal open) and always use the latest version.
   * Change `latest` to [a specific version](https://hub.docker.com/repository/registry-1.docker.io/radeklat/todoist-habitica-sync/tags) if you don't always want the latest version.


Add `--detach` flag to run in the background. You can close the terminal. The service should start on system start up.
* To see the log of the service running in the background, run `docker-compose logs todoist-habitica-sync`
* To stop the service, run `docker-compose stop todoist-habitica-sync`

# Update

## As a script

1. Open terminal
2. Navigate to the project repository:
   ```shell script
   cd todoist-habitica-sync
   ```
3. Update the source code:
   ```shell script
   git pull
   ```

4. Update application dependencies:
   ```shell script
   poetry install --only main
   ```
5. Run the application again.

## From docker-compose

If you used the `latest` tag, run:

```shell script
docker compose stop  # stop the container
docker compose pull todoist-habitica-sync  # pull a new image
docker compose up -d --force-recreate  # re-create container with the new image
docker image prune -f  # clean up unused images
```

# Environment variables
<!-- settings-doc start -->
## `TODOIST_USER_ID`

*Optional*

See "user_id" mentioned in a link under "Calendar Subscription URL" at https://todoist.com/prefs/integrations. Needed only for shared projects to score points for tasks owned by you.

## `TODOIST_API_KEY`

**Required**

See https://todoist.com/prefs/integrations under "API token".

## `HABITICA_USER_ID`

**Required**

See https://habitica.com/user/settings/api under "User ID".

## `HABITICA_API_KEY`

**Required**

See https://habitica.com/user/settings/api under "API Token", the "Show API Token" button.

## `SYNC_DELAY_MINUTES`

*Optional*, default value: `1`

Repeat sync automatically after N minutes.

## `DATABASE_FILE`

*Optional*, default value: `.sync_cache/sync_cache.json`

Where to store synchronisation details. No need to change.
<!-- settings-doc end -->

# Resetting sync cache

Sometimes certain changes require to reset the sync cache. The cache  holds state information only to allow recovery after an unexpected termination of the program. So it is not needed in between restarts and can be safely removed.

To reset the cache:
1. Stop the application.
2. Remove the `.sync_cache/sync_cache.json` file or any other location given in the [`DATABASE_FILE`](#database_file) config option.
3. Start to application again.
4. You should see all existing Todoist tasks loaded again either as `HIDDEN` (finished tasks) or `TODOIST_ACTIVE` (not completed yet).

# Planned work

* Synchronise overdue task to cause damage in habitica
* Parse difficulty from string (similar to p0-p4 in Todoist)
* Use @ Todoist labels for difficulty
