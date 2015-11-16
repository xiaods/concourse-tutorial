### 03 - Tasks extracted into resources

It is easy to iterate on a job's tasks by configuring them in the `pipeline.yml` as above. Eventually you might want to colocate a job task with one of the resources you are already pulling in.

This is a little convoluted example for our "hello world" task, but let's assume the task we want to run is the one from "01 - Hello World task" above. It's stored in a git repo.

In our `pipeline.yml` we add the tutorial's git repo as a resource:

```yaml
resources:
- name: resource-tutorial
  type: git
  source:
    uri: https://github.com/starkandwayne/concourse-tutorial.git
```

Now we can consume that resource in our job. Update it to:

```yaml
jobs:
- name: job-hello-world
  public: true
  plan:
  - get: resource-tutorial
  - task: hello-world
    file: resource-tutorial/01_task_hello_world/task_hello_world.yml
```

Our `plan:` specifies that first we need to `get` the resource `resource-tutorial`.

Second we use the `01_task_hello_world/task_hello_world.yml` file from `resource-tutorial` as the task configuration.

Apply the updated pipeline using `fly set-pipeline -t tutorial -c pipeline.yml -p 03_resource_job`. #TODO find out how to do that better

Note: `fly` has shorter aliases for it's commands, `fly sp` is shorthand for `fly set-pipeline`

Or run the pre-created pipeline from the tutorial:

```
cd ../03_resource_job
fly sp -t tutorial -c pipeline.yml -p 03_resource_job
fly unpause-pipeline -t tutorial -p 03_resource_job
```

![resource-job](http://cl.ly/image/271z3T322l25/03-resource-job.gif)

After manually triggering the job via the UI, the output will look like:

![job-task-from-wrapper](http://cl.ly/image/0Q3m223v2l3M/job-task-from-wrapper.png)

The `job-hello-world` job now has two steps in its build plan.

The first step fetches the git repository for these training materials and tutorials. This is a "resource" called `resource-tutorial`.

This resource can now be an input to any task in the job build plan.

The second step runs a user-defined task. We give the task a name `hello-world` which will be displayed in the UI output. The task itself is not described in the pipeline. Instead it is described in `01_task_hello_world/task_hello_world.yml` from the `resource-tutorial` input.

There is a benefit and a downside to abstracting tasks into YAML files outside of the pipeline.

The benefit is that the behavior of the task can be modified to match the input resource that it is operating upon. For example, if the input resource was a code repository with tests then the task file could be kept in sync with how the code repo needs to have its tests executed.

The downside is that the `pipeline.yml` no longer explains exactly what commands will be invoked. Comprehension is potentially reduced. `pipeline.yml` files can get long and it can be hard to read and comprehend all the YAML.

Consider comprehension of other team members when making these choices. "What does this pipeline actually do?!"

One idea is to consider how you name your task files, and thus how you name the wrapper scripts that they invoke.

Consider using (long) names that describe their purpose/behavior.

Try to make the `pipeline.yml` readable. It will become important orchestration within your team/company/project; and everyone needs to know how it actually works.

### 04 - Get job output in terminal

The `job-hello-world` had terminal output from its resource fetch of a git repo and of the `hello-world` task running.

You can also view this output from the terminal with `fly`:

```
fly -t tutorial watch -j 03_resource_job/job-hello-world
```

The output will be similar to:

```
Cloning into '/tmp/build/get'...
e8c6632 Added trigger: true to autostart both jobs after update.
initializing with docker:///busybox
running echo hello world
hello world
succeeded
```

### 05 - Trigger a Job via the Concourse API

Our concourse in vagrant has an API running at `http://192.168.100.4:8080`. The `fly` CLI targets this endpoint by default.

We can trigger a job to be run using that API. For example, using `curl`:

```
curl http://192.168.100.4:8080/pipelines/03_resource_job/jobs/job-hello-world/builds -X POST
```

You can then watch the output in your terminal using `fly watch` from above:

```
fly -t tutorial watch -j 03_resource_job/job-hello-world
```

