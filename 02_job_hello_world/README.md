# 02 - Hello World job

```
cd ../02_job_hello_world
fly set-pipeline -t tutorial -c pipeline.yml -p 02helloworld
fly unpause-pipeline -p 02helloworld
```

It will display the concourse pipeline (or any changes) and request confirmation:

```yaml
jobs:
  job job-hello-world has been added:
    name: job-hello-world
    public: true
    plan:
    - task: hello-world
      config:
        platform: linux
        image: docker:///busybox
        run:
          path: echo
          args:
          - hello world
```

You will be prompted to apply any configuration changes each time you run `fly set-pipeline` (or its alias `fly sp`)

```
apply configuration? (y/n):
```

Press `y`.

You should see:

```
pipeline created!
you can view your pipeline here: http://192.168.100.4:8080/pipelines/02helloworld
```

Go back to your browser and start the job manually. Click on `job-hello-world` and then click on the large `+` in the top right corner. Your job will run.

![job](http://cl.ly/image/3i2e0k0v3O2l/02-job-hello-world.gif)

Clicking the top-left "Home" icon will show the status of our pipeline.

