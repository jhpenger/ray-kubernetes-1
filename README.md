# ray-kubernetes
Ray Framework (https://github.com/ray-project/ray) on Kubernetes

Here we provide instructions for getting Ray up-and-running on Kubernetes.

1. First install `gcloud`. To do this on a Mac, follow the instructions
[here](https://cloud.google.com/sdk/docs/quickstart-mac-os-x). To do this on
Ubuntu, do the following.

  a. `wget https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-143.0.1-linux-x86_64.tar.gz `
  b. `./install.sh` (you will probably get an error message, in which case you
     should do`sudo ./install.sh`).
  c. If you had to use `sudo`, then you will probably get permission errors in
     the next step, so you may be able to fix this by doing something like
     `sudo chown -R /home/ubuntu/.config`.

2. Set up gcloud tools.

  a. Do `gcloud init`
  b. Do `gcloud components install kubectl`

3. You'll also need a running cluster, e.g., on GCE. For example, you can do the
   following.

  a. Make a GCE account.
  b. Click on the menu in the upper left, and click "Container Registry".
  c. Click "Create Cluster", and make a cluster.
  d. Go back to the menu and click "Compute Engine".
  e. Click "Create Instance".

    i. Put it in the same region as the cluster.
    ii. Switch the OS to Ubuntu 16.04 (and use SSD and with 25GB).
    iii. Click "allow full access to all Cloud APIs".

4. Associate kubectl with the cluster.

  a. `gcloud container clusters get-credentials cluster-1`
  b. `gcloud auth application-default login`
  c. Try `kubectl` and make sure that works. The command `kubectl get pods`
     should also work (but return nothing), and `kubectl get nodes` should show
     you some nodes.

5. Install Docker locally. If you're doing it on Ubuntu, make sure to
   [these instructions](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04).

  a. In particular, don't forget to run `sudo usermod -aG docker $(whoami)`.
     You'll probably need to refresh your path after that.
  b. Try `docker ps`. That should work.

6. Clone the `ray-kubernetes` repository.

  a. Edit `build.sh` to use the name of your project instead of
     `prophet-158422`.
  b. Run `build.sh`.
  c. Push the build to google's registry with
     `gcloud docker push us.gcr.io/raytest-140921/ray`.
  d. Edit `head.yml` to use your project name instead of `prophet-18422`.
  e. Edit `worker.yml` to use your project name instead of `prophet-18422`.

7. Launch things.

  a. Start the head with `kubectl create -f head.yml`. Verify that
     `kubectl get pods` returns some stuff. Note that
     `kubectl describe pod ray-head` shows some useful information. Check
     `kubectl logs ray-head`.
  b. Start the workers with `kubectl create -f worker.yml`.
  c. Connect to the head with `kubectl exec -it ray-head bash`.
  d. In Python on the head, run
     `socket.gethostbyname("ray-head.default.svc.cluster.local")` to get the
     IP address of the head node. You can use this in `ray.init` to connect to
     the cluster.
