# ITX BACKEND PLATFORMS

- [Platforms Set Up](#platforms-set-up)
- [Platforms Structure](#platforms-structure)
- [WEB APP Installation](#web-app-installation)

## <a id="platforms-set-up"></a>Platforms set up

Read the `.env.example` comments that explains the variable usage target so building all the containers required can be automated.

- Copy `.env.example` *(or the `.env.example-clean`)* to `.env` and adjust platforms settings (webapp port, grafana port, k6 port, container RAM usage, etc.). The example environment file has already tested the minimum required resources

- By default, your local machine port 5000 will be used for the project as it is required by the exam. The other ports are editable.

- You can now clone the WEB APP repository before continuing. Follow the [WEB APP Installation](#web-app-installation)
<br>

### Automation by Makefile

There is a make recipe that helps you to find out each recipe content bounded to its platform
```sh
$ make help

Usage: $ make [target]
Targets:
$ make help                           shows this Makefile help message
$ make local-hostname                 shows local machine ip and container ports set
$ make local-ownership                shows local ownership
$ make local-ownership-set            sets recursively local root directory ownership
$ make webapp-hostcheck               shows this project ports availability on local machine for webapp container
$ make webapp-info                    shows the webapp docker related information
$ make webapp-set                     sets the webapp enviroment file to build the container
$ make webapp-create                  creates the webapp container from Docker image
$ make webapp-network                 creates the webapp container network - execute this recipe first before others
$ make webapp-ssh                     enters the webapp container shell
$ make webapp-start                   starts the webapp container running
$ make webapp-stop                    stops the webapp container but its assets will not be destroyed
$ make webapp-restart                 restarts the running webapp container
$ make webapp-destroy                 destroys completly the webapp container

$ make repo-flush                     echoes clearing commands for git repository cache on local IDE and sub-repository tracking remove
$ make repo-commit                    echoes common git commands
```

The Docker container for the application has its own `./platforms/nginx-nodejs-25/docker/.env` file, but it is created automatically by the `./.env` that handles the required variable values. Thus, this is the first Make recipe to be executed
```sh
$ make webapp-set
```

Now, you can build and run the application Docker container
```sh
$ make webapp-create
```

To know each container information you can execute `$ make webapp-info` *(except k6 container that is not continuously running)* or by each container, as e.g. the API:
```sh
$ make webapp-info
ITX FRONTEND EXAM: NGINX - NODEJS 25
Container ID.: fb3e8a7f0735
Name.........: itx-front-pr-webapp-dev
Image........: itx-front-pr-webapp-dev:alpine3.23-nginx-nodejs25
CPUs.........: 2.00
RAM..........: 512M
SWAP.........: 1G
Host.........: 127.0.0.1:7325
Hostname.....: 192.168.1.41:7325
Docker.Host..: 172.20.0.2
NetworkID....: cf3432dd2cbc985b473de0d165bbe3a9109e0c198a5688fa4cad50ce739b82e6
```


To follow the project evaluation, follow the WEB APP README.md, once the repository has been cloned, inside the `./application` directory.

Don't forget to follow the WEB APP cloning repository documentation: [WEB APP Installation](#web-app-installation)
<br><br>

## <a id="platforms-structure"></a>Use this Platform Repository for your own WEB APP repositories

Repository directories structure overview:
```bash
.
├── application     # Core directory binded in Docker main container for front-end
│   └── ...         # sub-module or detach with the real project respository
│
├── platforms
│   └── nginx-nodejs-25
│       ├── LICENSE
│       ├── Makefile
│       ├── README.md
│       └── docker
│           ├── Dockerfile
│           ├── config
│           │   ├── nginx
│           │   │   ├── conf.d
│           │   │   │   └── default.conf            # nginx default server block
│           │   │   ├── conf.d-sample
│           │   │   │   ├── default-proxy.conf
│           │   │   │   └── default-spa.conf
│           │   │   └── nginx.conf
│           │   ├── nodejs
│           │   │   ├── conf.d
│           │   │   └── conf.d-sample
│           │   │       └── default.conf
│           │   ├── ownerships.sh
│           │   └── supervisor
│           │       ├── conf.d                      # supervisor services
│           │       │   └── nginx.conf
│           │       ├── conf.d-sample
│           │       │   ├── nginx.conf
│           │       │   ├── nodejs-dev.conf
│           │       │   ├── nodejs-index.conf
│           │       │   └── nodejs-spa.conf
│           │       ├── supervisord.conf
│           │       └── supervisord.conf.sample
│           ├── docker-compose.network.yml
│           └── docker-compose.yml
│
├── resources
│   ├── application     # webapp backup script
│   ├── automation
│   │   ├── local
│   │   │   ├── Makefile
│   │   │   └── Makefile.child
│   │   └── remote
│   │       └── ...
│   └── docs
│       └── images
│           └── ...
│
├── .env          # Platforms main values applied
├── .env.example  # Platforms main values example
├── Makefile      # Automated commands into recipes
└── README.md
```
<br>

## <a id="web-app-installation"></a>Managing the `./application` as Detached Repository

Here’s a step-by-step guide for using this Platform repository along with the WEB APP repository. The approach applied to manage the WEB APP project is as detached repository in other to separate the concernes of front-end from platform maintenance.

#### **GIT Detached Repository (Recommended)**

> Git commands can be executed **whether from inside the container or on the local machine**.

- Remove `application` from git cache:
  ```bash
  $ git rm -r --cached -- "application/*" ":(exclude)application/.gitkeep"
  $ git clean -fd
  $ git reset --hard
  $ git commit -m "maint: application directory and its default installation removed"
  ```

- Clone the desired repository as a detached repository:
  ```bash
  $ git clone https://github.com/pabloripoll/itx-frontend-application ./application
  ```

- The `./application` directory is now an **independent repository**, not tracked as a submodule in your main repo. You can use `git` commands freely inside `./application` from anywhere.
<br>

#### **Summary Table**

| Approach         | Repo independence | Where to run git commands  | Use case                              |
|------------------|------------------|-----------------------------|---------------------------------------|
| Submodule        | Tracked by main  | Inside container            | Main repo controls webapp version   |
| Detached (rec.)  | Fully independent| Local or container          | Maximum flexibility                   |

> **Note**: After new project cloned inside `./application`, consider adding `./application/.gitkeep` in it to prevent accidental tracking *(especially for detached repository)*.

<br>

## License

This project is open-sourced under the [Apache license](LICENSE).

<!-- FOOTER -->
<br>

---

<br>