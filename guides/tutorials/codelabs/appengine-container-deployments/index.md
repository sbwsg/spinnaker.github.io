---
layout: single
title: "App Engine Flex Custom Container Deployments"
sidebar:
  nav: guides
---

{% include toc %}

This codelab will walk you through the process of pushing a new docker container
to Google Container Registry and using Spinnaker to deploy that container to
App Engine.

# Prerequisites
This codelab assumes you have a billing-enabled GCP project and the
[gcloud tool installed](https://cloud.google.com/sdk/gcloud/). You'll also need to
be running docker locally in order to build the container image.

# Configure and Deploy Your Spinnaker Instance
If Spinnaker is not yet configured, follow this [Halyard quickstart](https://www.spinnaker.io/setup/quickstart/halyard-gce/),
then [configure the GAE cloud provider](https://www.spinnaker.io/setup/install/providers/appengine/).

# Build and Push a Docker Image to Google Container Registry
In this step you'll create and push a docker image to Google Contaienr Registry. That image
will be what eventually gets deployed to App Engine. Run the following commands in a terminal
(don't forget to replace `<your gcp project>` in these commands with the name of your GCP project!):

```bash
# Take a clone of the demo's code repository
git clone https://github.com/sbwsg/spinnaker-gae-flex-custom-hello-world.get

# Change to the directory that was just created
cd spinnaker-gae-flex-custom-hello-world

# Build the app container and tag it with your project's URL
docker build . --tag gcr.io/<your gcp project>/my-hello-world-app:dev

# Push the container go GCR
gcloud docker -- push gcr.io/<your gcp project>/my-hello-world-app:dev
```

This docker image is now available in your Google Container Registry and the URL
can be used to deploy from a Spinnaker pipeline.

# Create a Spinnaker Pipeline To Deploy the Image
In this step you'll create a Spinnaker Pipeline that deploys the docker image from
Google Container Registry to Google App Engine.

1. Open Spinnaker in your browser.
2. Click on the Applications tab at the top of the page.
3. Select your Google App Engine application from the list that appears.
4. Click on the Pipelines link that appears at the top of the page
5. If you don’t have any pipelines configured yet then click on “Configure a new pipeline”, otherwise click on the “+ Create” button that appears in the top right corner.
6. Select the Type “Pipeline”
7. Name the pipeline “GAE Flex Demo”
8. Click “Add stage”
9. Choose Type: “Deploy”
10. Set the Stage Name to be “deploy container from registry”
11. Click on “Add server group”
12. Select your App Engine account in the Account dropdown.
13. Click on Source Type of “Container Image” and set Resolve URL to “via text input”
14. Enter the URL of the docker container you pushed earlier: `gcr.io/<your gcp project>/my-hello-world-app:dev`
15. In the next section, Config Files, click “Add Config File”
16. Enter text:

    ```yaml
    runtime: custom
    env: flex
    ```

17. Click Add
18. Click the “Save Changes” button that appears in the bottom right corner
19. Go back to the Pipelines screen by clicking on the Pipelines link at the top of the page
20. Click on the “Start Manual Execution” link on the pipeline
