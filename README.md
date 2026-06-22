# CI/CD Final Project

This is my final project for IBM's **CI/CD Tools and Practices** course on Coursera.
I took a Flask microservice and built a complete CI/CD pipeline around it, so every
change gets tested, linted, built, and deployed automatically instead of me doing it
by hand.

## What I built

- A Flask microservice in `service/` with unit tests in `tests/`
- A **CI workflow** using GitHub Actions that runs automatically on every push and
  pull request to `main`
- A **CD pipeline** using Tekton that builds the image and deploys it to OpenShift
- A `Dockerfile` to containerize the service
- A `bin/setup.sh` script that sets up the whole dev environment (Python 3.9,
  virtual env, Selenium/Chrome for BDD testing) in one go

## CI - GitHub Actions

Defined in `.github/workflows/workflow.yml`. On every push or PR to `main`, it:

1. Checks out the code
2. Installs dependencies from `requirements.txt`
3. Lints with **flake8** (fails the build on syntax errors and undefined names,
   flags complexity over 10 and lines over 127 chars)
4. Runs the unit tests with **nose**, with coverage on the `app` package

## CD - Tekton Pipeline

Defined in `.tekton/`. The pipeline runs as a chain of tasks:

1. **cleanup** — wipes the workspace so the build starts clean
2. **git-clone** — clones the repo at the given revision
3. **flake8** - lints the code (max line length 120)
4. **nose** - runs the unit tests
5. **buildah** - builds the container image from the Dockerfile
6. **deploy** - uses the OpenShift client to create/update the deployment with the
   new image

Each step only runs if the one before it passes — so a failed lint or test stops
the deploy from happening.

## Tech stack

Python · Flask · GitHub Actions · Tekton · Buildah · OpenShift · Docker · flake8 · nose

## What I learned

- Setting up GitHub Actions so every commit gets linted and tested automatically
- Writing Tekton Tasks and Pipelines as Kubernetes-native CI/CD, including chaining
  tasks with `runAfter`
- Building container images with Buildah and deploying them to OpenShift through
  the pipeline instead of manually
- The full loop: code change → lint → test → build → deploy, with no manual steps
  in between


