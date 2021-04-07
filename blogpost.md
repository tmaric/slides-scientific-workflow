# Build a custom Docker Image for the OpenFOAM project

System libraries vs. ThirdParty dependencies.

* Push the image to dockerhub

```
sudo docker login -u USER -p PASSWORD 
sudo docker push  tmaric/openfoam-v2012_ubuntu-focal
```

# Make sure the GitLab Runner uses your local image 

Promted for default image `image = "openfoam-v2012_ubuntu-focal"`. 

A pull policy must be configured, otherwise the runner will only search DockerHub: 

https://docs.gitlab.com/runner/executors/docker.html#how-pull-policies-work

> To do this, you modify a file called config.toml, which uses the TOML format.

In `/etc/gitlab-runner/config.toml`, add `pull_policy = never` if you won't release your own Docker images to DockerHub or GitLab's container registry.  


```
[[runners]]
  name = "REPO-MACHINE"
  url = "LINK"
  token = "Shhhh"
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "NAME-OF-YOUR-IMAGE"
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
    pull_policy = "never" 
```

**Test the changes to .gitlab-ci.yaml locally on a machine that has the GitLab runner installed and the image available, this way you save time: 

```
    sudo gitlab-runner exec docker --docker-pull-policy never build
```

and don't introduce 200 CI debug commits in the repo, like I did.


```
WARNING: You most probably have uncommitted changes. 
WARNING: These changes will not be tested.
```

remotely, they won't be tested remotely, they will be tested locally, where you want them to be tested, because you're debugging locally. The runner takes .gitlab-ci.yaml in its modified state and runs it.

# Add the build CI job 

# Add the convergnece CI job 

* Run the parameter variation 

# Add the visualization CI job 

* Run the jupyter notebooks in batch mode  
* export artifacts:
    - notebook as: Jupyter notebook for interactive calculation (local), HTML, Markdown, PDF, ...
    - CSV and .TEX in pandas.MultiIndex format 

* On main branch: if all tests are GREEN publish archiv on Zenodo?

* Read about [SSH keys using the Docker executor](https://docs.gitlab.com/ee/ci/ssh_keys/)

- Store the private SSH key of the user running the GitLab runner on the test machine as CI Variables (Project -> Settings -> CI -> Variables)
- Add the [before_script](https://docs.gitlab.com/ee/ci/ssh_keys/) to your .gitlab-ci.yaml
- Add the *public* SSH key as a deploy key to the GitLab Pages repository. 
- Add the GitLab server to the list of known hosts in the Docker executor Gitlab Runner 


