# CSCI-4308-TerumoBCT
Repository for weekly timesheets/attendance records, project submissions, and source code for sponsor.

## Start Docker Containers

The runtime for CSCI-4308-TerumoBCT is inside a Docker container. We have a `make` command to launch the appropriate containers.  To launch the docker containers and begin working on a CPU, run from the root directory of the repository:
```bash
make dev-start
```


This builds images using the Dockerfile in docker/Dockerfile, and runs the containers. To see the running containers, run
`docker ps`

You should see two containers running (example below).  On your machine the container ids and the names of the images and running containers will be different, i.e. they will have your username rather than derekthomas.  In addition, the local ports will be different as well. That is expected. 
```bash
CONTAINER ID        IMAGE                         COMMAND                  CREATED             STATUS              PORTS                       NAMES
f0fbf5728341        csci-4308-terumobct_jupyter   "bash -c 'cd /mnt &&…"   5 seconds ago       Up 4 seconds        127.0.0.1:32781->8888/tcp   CSCI-4308-TerumoBCT_jupyter_derekthomas
3a95ccdcaafb        csci-4308-terumobct_bash      "/bin/bash"              15 seconds ago      Up 14 seconds                                   CSCI-4308-TerumoBCT_bash_derekthomas
```

There is also a simple make command to easily stop the containers associated with the project:
```bash
make dev-stop
```

## Makefile

Use `make` to run most of the typical developer operations, e.g. `make dev-start`, etc.  For a full list of make commands, run:
```bash
make help
```

The `make` commands supported out of the box are:
```
black                          Runs black auto-linter
ci-black                       Test lint compliance using black. Config in pyproject.toml file
ci                             Check black and flake8
dev-start                      Primary make command for devs, spins up containers
dev-stop                       Spin down active containers
git-tag                        Tag in git, then push tag up to origin
isort                          Runs isort to sorts imports
lint                           Lints repo; runs black and isort on all files
```

## Using the Containers

The docker-compose is setup to mount the working directory of the repository into each of the containers.  That means that all change you make in the git repository will automatically show up in the containers, and vice versa. 

The typical workflow is to do all text editing and git commands in the local host, but *run* all the code inside the container -- either in the bash executor or the Jupyter notebook.  As mentioned earlier, the containers are the *runtime* -- they have a consistent operating system (Ubuntu), drivers, libraries, and dependencies.  It ensures that the runtime is consistent across all developers and compute environments -- from your local laptop to the cloud.  

### Bash Executor

This is the container that you should go into to run experiments, e.g. etl, training, evaluation, prediction.  Use the command below to go into the container from the command line: 
```bash
docker exec -it <bash-executor-container-name> /bin/bash
```
### Jupyter Server
This is the container that you should browse to in your web browser to access Jupyter notebooks.  You should go to the local port that the Jupyter server is mapped to access the standard Jupyter notebook UI, e.g. `http://localhost:<jupyter_port>`.  You can look up that port using the `docker ps` command.  In the example above, the user should browse to `http://localhost:32781` to see the UI. 


# Workflow

In this section we document workflow. 

## Step 1: Get the Data

TerumoBCT plans to send us data by cloning an sql database. For this purpose we have setup a google cloud sql instance to handle it.
The following python can be used to query the instance:

```python
ssl_args = {'ssl_ca':'server-ca.pem','ssl_cert':'client-cert.pem','ssl_key':'client-key.pem'}
engine = db.create_engine('mysql+mysqlconnector://root:<password>@34.66.181.115/bct_data', connect_args=ssl_args)
```
While you can query the DB as much as you want, if you have the spare hdd space you may want to make a local copy, in that case put the data in `data/raw/` 


# Project Structure
```
├── Admin                	  <- Folder for timesheets and team administration
├── README.md                 <- Project README
├── data                      <- Data cache folder
│   ├── external              <- Data from third party sources.
│   ├── interim               <- Intermediate data that has been transformed.
│   ├── processed             <- The final, canonical data sets for modeling.
│   └── raw                   <- The original, immutable data dump.
├── docker
│   ├── Dockerfile            <- Project Dockerfile
│   ├── docker-compose.yml    <- Docker Compose configuration file
│   └── requirements.txt      <- The requirements file for reproducing the analysis environment.
│                                New libraries should be added in the requirements
├── experiments               <- Where to store different model experiments, e.g., model pkls and analysis
├── notebooks                 <- Jupyter notebooks. Naming convention is a number (for ordering),
│                                the creator's initials, and a short `-` delimited description, e.g.
│                                `1.0-dwt-initial-data-exploration`.
├── pyproject.toml            <- Config file used by black
├── Sponsor					  <- Sponsor stuff?
├── Submissions				  <- Submissions for class
├── tox.ini                   <- tox config file with settings for flake
├── Makefile                  <- Makefile for starting and stopping containers, lint, and local CI.
└── CSCI_4308_TerumoBCT   <- Project repo
    ├── __init__.py           <- Makes repo a Python module
    ├── features              <- Feature engineering pipeline go here 
    ├── models                <- Model pipelines go here   
    ├── scripts               <- Python executable scripts
    ├── util                  <- Util folder
    └── viz                   <- Visualization functions
```
--------

# Developer Workflow

### Lint

[Linting](https://realpython.com/python-code-quality/) -- To make things simple, we have created a script that automates linting as much as possible. It can be invoked:
```bash
make lint
```

This should use black and isort to automatically format your code so that it passes CI. If it is unable to, it will let you know. You can check if CI passes after auto-formatting by running the `ci.sh` script above. 

## Adding new libraries

The Dockerfile also installs any project specific libraries from docker/requirements.txt. When you want to add a new library,
add it to the requirements.txt files, e.g, my_lib==x.x.x. 

```bash
make dev-start
```

Or build an image directly, go to the docker folder `cd docker`, and run

```bash
docker build -t image_name:tag .
```

Where image_name and tag are whatever you want to name and tag your image.

