# In GCB, can we run kubectl to deploy images that haven't yet been pushed to the registry?

### Prerequisites:
- A running GKE cluster
- GCB service account has role 'Kubernetes Engine Admin'

### Goal:
We're trying to build a container image, then deploy it to a GKE cluster.

### Instructions:
1. Clone this repo
2. Run `gcloud builds submit --config=push-first.cloudbuild.yaml .`
  - This build has an explicit 'push' step after `docker build` and before `kubectl run`.
  - The image is successfully deployed to GKE
3. Run `gcloud builds submit --config=push-last.cloudbuild.yaml .`
  - This build uses the 'images' tag to push the built image to GCR.
  - The build will complete without error, but the result of `kubectl get pods` reports status ErrImagePull
    - ...because the image is not yet available in the registry
    - However, the deploy will *eventually* succeed, after the build completes and the image gets pushed by the 'images' tag
    - But that means we can't deploy an application, and then access it, within one build. We need an explicit push step.