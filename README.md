# Push to DigitalOcean GitHub Action

## Description

This GitHub Action pushes a Docker image to DigitalOcean Container Registry. It also handles tagging and versioning of the Docker image based on the branch and environment.

## Inputs

- `digitalocean-token` (required): The DigitalOcean token needed to authenticate and push the Docker image.
- `digitalocean-repository` (required): The DigitalOcean repository where the Docker image will be pushed.

## Environment Variables

- `BRANCH_NAME`: The current branch name. This value is automatically obtained from the GitHub Actions context.
- `ENVIRONMENT`: The environment determined based on the branch name (`production` for `main`, `development` for `development`, `staging` for `staging`).
- `VERSION`: The Docker image version set to the current UTC date and time.

## Steps

1. **Set environment variables**: Determines the `BRANCH_NAME` and sets the `ENVIRONMENT` variable based on the branch name.
2. **Debug environment variables**: Prints the `ENVIRONMENT` and `BRANCH_NAME` to the console for debugging purposes.
3. **Login to DigitalOcean Container Registry**: Authenticates with DigitalOcean Container Registry using the provided token.
4. **Set Docker image version**: Sets the `VERSION` variable to the current UTC date and time.
5. **Debug Docker version**: Prints the `VERSION` to the console for debugging purposes.
6. **Pull existing images from DigitalOcean**: Pulls the existing Docker image for the environment and tags it as `rollback` if it exists.
7. **Build Docker image for DigitalOcean**: Builds the Docker image and tags it as the current version and latest.
8. **Debug build info**: Prints information about the built Docker image.
9. **Run Docker container to test for DigitalOcean**: Runs a Docker container to test the built image and then removes the test container.
10. **Push Docker image to DigitalOcean**: Pushes the Docker image to DigitalOcean Container Registry with the current version, latest, and rollback tags if applicable.
11. **Debug info**: Prints additional debug information and pushes the images again for verification.

## Sequence Diagram

```mermaid
sequenceDiagram
    participant GitHub Actions
    participant DigitalOcean
    participant Docker Container

    GitHub Actions->>GitHub Actions: Set environment variables
    GitHub Actions->>GitHub Actions: Debug environment variables
    GitHub Actions->>DigitalOcean: Login to DigitalOcean Container Registry
    GitHub Actions->>GitHub Actions: Set Docker image version
    GitHub Actions->>GitHub Actions: Debug Docker version
    GitHub Actions->>DigitalOcean: Pull existing images
    GitHub Actions->>GitHub Actions: Build Docker image
    GitHub Actions->>GitHub Actions: Debug build info
    GitHub Actions->>Docker Container: Run Docker container to test
    Docker Container->>GitHub Actions: Remove test container
    GitHub Actions->>DigitalOcean: Push Docker image
    GitHub Actions->>GitHub Actions: Debug info and push images again
```

## Usage Example

```
name: CI

on:
  push:
    branches:
      - main
      - development
      - staging
  pull_request:
    branches:
      - main
      - development
      - staging

jobs:
  push-digital-ocean:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Run Push to DigitalOcean Action
        uses: ronihdzz/push-to-digitalocean-action@main
        with:
          digitalocean-token: ${{ secrets.DIGITALOCEAN_TOKEN }}
          digitalocean-repository: ${{ vars.DIGITALOCEAN_REPOSITORY }}
```

## Notes

* Ensure you add `DIGITALOCEAN_TOKEN` as a secret in your GitHub repository settings.
* This workflow uses Docker to build and push images, so Docker must be installed and configured on the GitHub Actions runner.
