# Jenkins CI/CD Pipeline - DevOps Task 2

This sample uses Jenkins and Docker to build, smoke-test, and deploy a small static web app. The pipeline is defined in `Jenkinsfile` using Declarative Pipeline syntax.

## Repository files

- `Jenkinsfile` - Build, Test, and Deploy stages.
- `Dockerfile` - Packages the static page in Nginx.
- `index.html` - The sample app served by Nginx.
- `.dockerignore` - Excludes repository metadata and this README from the Docker build context.

## Requirements

- A Jenkins controller and an agent with the Pipeline plugin, Git support, Docker CLI, and permission to access the Docker daemon.
- A Git repository containing these files.
- Port `8081` available on the Docker host for the deployed app. Jenkins uses `8080` by default.

The Jenkins agent must be able to run `docker` commands. For a production setup, use a dedicated deployment agent with narrowly scoped access to the Docker host; access to the Docker daemon is highly privileged.
