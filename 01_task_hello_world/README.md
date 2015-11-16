# 01 - Hello World task

```
cd 01_task_hello_world
fly -t tutorial execute -c task_hello_world.yml
```

The output starts with

```
executing build 1
initializing with docker:///busybox
```

Every task in Concourse runs within a "container" (as best available on the target platform). The `task_hello_world.yml` configuration shows that we are running on a `linux` platform using a container image defined by `docker:///busybox`.

Within this container it will run the command `echo hello world`:

```yaml
---
platform: linux

image: docker:///busybox

run:
  path: echo
  args: [hello world]
```

At this point in the output above it is downloading a Docker image `busybox`. It will only need to do this once; though will recheck every time that it has the latest `busybox` image.

Eventually it will continue:

```
running echo hello world
hello world
succeeded
```

By changing the values for `image:` and `run:`, we can have
concourse run a different task. For convenience, a task file with
these changes is provided for you in `task_ubuntu_uname.yml`:

```yaml
---
platform: linux

image: docker:///ubuntu#14.04

run:
  path: uname
  args: [-a]
```

Now we can execute this new task on the pipeline:
```
$ fly -t tutorial execute -c task_ubuntu_uname.yml
executing build 2
initializing with docker:///ubuntu#14.04
running uname -a
Linux 5prpv3fp5op 3.19.0-30-generic #33~14.04.1-Ubuntu SMP Tue Sep 22 09:27:00 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
succeeded
```

A common pattern is for Concourse tasks to `run:` wrapper shell scripts, rather than directly invoking commands. When using wrapper scripts like this and as you build up into complex pipelines, __you will appreciate giving your task files and wrapper shell scripts the same base name__.

Following this pattern, in the `01_task_hello_world` folder you will find these two files:

-	`task_show_uname.yml`
-	`task_show_uname.sh`

When you execute a task file directly via `fly`, it will upload the current folder as an input to the task. This means the wrapper shell script is available for execution:

```
$ fly -t tutorial execute -c task_show_uname.yml
executing build 3
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 10240    0 10240    0     0  2780k      0 --:--:-- --:--:-- --:--:-- 3333k
initializing with docker:///busybox
running ./task_show_uname.sh
Linux 5prpv3fp5or 3.19.0-30-generic #33~14.04.1-Ubuntu SMP Tue Sep 22 09:27:00 UTC 2015 x86_64 GNU/Linux
succeeded
```

The output above `running ./task_show_uname.sh` shows that the `task_show_uname.yml` task delegated to a wrapper script to perform the task work.

The `task_show_uname.yml` task is:

```yaml
platform: linux
image: docker:///busybox

inputs:
- name: 01_task_hello_world
  path: .

run:
  path: ./task_show_uname.sh
```

The new concept above is `inputs:`. 

In order for a task to run a wrapper script, it must be given access to the wrapper script. In order for a task to process data files, it must be given access to those data files. In Concourse these are referred to as `inputs` to a task.

Consider the `inputs:` snippet above:

```yaml
inputs:
- name: 01_task_hello_world
  path: .
```

This is saying:

1.	I want to receive an input folder called `01_task_hello_world`. Given that we are running the task directly from the `fly` CLI, and we're running it from our host machine inside the `01_task_hello_world` folder, then the contents of the current host machine folder will be uploaded to Concourse.
2.	I want its contents to be placed in the folder `.` (that is, the root folder of the task when its running)

_A side note: If no `path` is specified for an input, then the contents of the input will be placed into a folder at `./<input_name>`, where input_name is the `name` of the input._

Later when we look at Jobs with inputs, tasks and outputs we'll return to passing `inputs` into tasks within a Job.

Given the list of `inputs`, we now know that the `task_show_uname.sh` script (which is in the same folder) will be available in the root folder of the running task. This allows us to invoke it at this known location:

```yaml
run:
  path: ./task_show_uname.sh
```
