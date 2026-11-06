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

## Run locally first

From this directory:

```sh
docker build -t devops-task2-app:local .
docker run --rm -p 8081:80 devops-task2-app:local
```

Open `http://localhost:8081`. Stop the container with `Ctrl+C`.

## Configure Jenkins

1. Push the files to a new GitHub repository.
2. In Jenkins, create a **Multibranch Pipeline** and connect it to the repository. Configure repository credentials if it is private.
3. Ensure the agent that runs the job has Docker installed and can access the Docker daemon. The Docker CLI alone is not enough if no daemon is reachable.
4. Scan the repository. Jenkins discovers the `Jenkinsfile` and creates a job for each branch containing it.
5. Under **Scan Multibranch Pipeline Triggers**, enable **Periodically if not otherwise run** and choose an interval such as five minutes. This lets Jenkins discover commits without exposing the Jenkins port publicly to a webhook.
6. Push a commit to run the pipeline. Builds and tests run for branches; deployment runs only on the `main` branch.

## Pipeline behavior

1. **Build** creates a versioned Docker image tagged with the Jenkins build number.
2. **Test** starts that image and checks that its HTTP response contains `DevOps Task 2`. The test container is removed in the `post` section.
3. **Deploy** runs only for `main`, tags the tested image as `latest`, replaces the previous container, and serves the app on port `8081` of the Docker host. Jenkins itself uses port `8080` by default, so the app uses `8081` to avoid a port conflict on this single-server setup.

This is a learning example. It deploys to the same Docker host used by the Jenkins agent and does not push the image to a registry. For real environments, deploy to a separate target and use a registry plus Jenkins credentials for registry authentication.

## Interview questions

1. **What is Jenkins, and how is it used in CI/CD?**
   Jenkins is an automation server. In CI/CD, it checks out source code, runs build and test steps, and can package and deploy successful changes.

2. **What is a Jenkinsfile?**
   A Jenkinsfile is a text file stored with the project source that defines a Jenkins Pipeline as code. The expected spelling is `Jenkinsfile` (one word, capital J).

3. **How do you create and configure Jenkins pipelines?**
   Add a Jenkinsfile to the repository, create a Pipeline or Multibranch Pipeline job, connect the source repository and credentials, configure commit triggers, and ensure the selected agent has the needed tools and permissions.

4. **What are common stages in a Jenkins pipeline?**
   Common stages include checkout, build, test, security or quality checks, package, and deploy. This sample uses Build, Test, and Deploy.

5. **What is the difference between Declarative and Scripted Pipelines?**
   Declarative Pipeline uses a structured `pipeline {}` syntax with defined sections and validation. Scripted Pipeline uses Groovy code inside `node {}` and allows more flexible control flow, with greater responsibility for structure.

## Screenshot evidence

After a successful run, capture the Jenkins stage view showing Build, Test, and Deploy, plus the running page at `http://<docker-host>:8081`. Add those screenshots to the repository if your submission requires them.
