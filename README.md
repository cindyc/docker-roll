# Docker Roll

Docker Roll is a service that manages CI workflow using Docker.

How it works:

Containers have roles: builder, packager, test-runner, deployer, db-couchdb, db-neo4j, simulator, etc. There are long running containers and on-spot containers.

A pod runs dr-engine (docker-roll-engine) with a registry service that’s the controller of the following services:

A workflow manager processes workflow configurations: workflows are defined by users in YAML files, each workflow consists of jobs, and each job consists of a set of steps. A job defines its required capacities (os version, tools, installed packages) and required resources (artifacts, data, files). The workflow manager maps capacities to container roles and resource specs to resource managers,  it also plans the steps based on their dependency relationships —steps that has inter-dependencies are scheduled in order, and steps without dependencies can be scheduled simultaneously.

A scheduler service dispatches jobs to a container or a cluster of containers through Swarm and/or Kubernetes, and tracks the state of these jobs. It reports back to the docker-roll-engine the status of the jobs and the resources being added by the jobs.

A resource manager that manages the resources: it checks the availability of the resources on the shared storage or resources that’s being created by other jobs in other containers (e.g. a builder job building an artifact), and report the resource availability back to the dr-engine.

A matcher service manages linking the containers:  (e.g. a builder containers that has finished building the artifacts can be mounted as data volume to a packager container), or linking a database container to a test-runner container.
