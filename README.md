Concourse Tutorial
==================

Learn to use https://concourse.ci with this linear sequence of tutorials. Learn each concept that builds on the previous concept.

Getting started
---------------

Install Vagrant/Virtualbox.

Fetch this tutorial and start a server

```
git clone git@github.com:starkandwayne/concourse-tutorial.git
cd concourse-tutorial
vagrant up
```

Open http://192.168.100.4:8080/ in your browser:

[![initial](http://cl.ly/image/401b2z2B3w17/no-pipelines.png)](http://192.168.100.4:8080/)

Once the page loads in your browser, click to download the `fly` CLI appropriate for your operating system:

![cli](http://cl.ly/image/1r462S1m1j1H/fly_cli.png)

Once downloaded, copy the `fly` binary into your path (`$PATH`), such as `/usr/local/bin` or `~/bin`. Don't forget to also make it executable. For example,
```
sudo mkdir -p /usr/local/bin
sudo mv ~/Downloads/fly /usr/local/bin
sudo chmod 0755 /usr/local/bin/fly
```

Target Concourse
----------------

In the spirit of declaring absolutely everything you do to get absolutely the same result every time, the `fly` CLI requires that you specify the target API for every `fly` request.

First, alias it with a name `tutorial` (this name is used by all the tutorial wrapper scripts):

```
fly --target tutorial login  --concourse-url http://192.168.100.4:8080 sync
```

You can now see this saved target Concourse API in a local file:

```
cat ~/.flyrc
```

Shows a simple YAML file with the API, credentials etc:

```yaml
targets:
  tutorial:
    api: http://192.168.100.4:8080
    username: ""
    password: ""
    cert: ""
```

When we use the `fly` command we will target this Concourse API using `fly -t tutorial`.

> @alexsuraci: I promise you'll end up liking it more than having an implicit target state :) Makes reusing commands from shell history much less dangerous (rogue fly configure can be bad)

Tutorials
---------


### 20 - Available concourse resources

https://github.com/concourse?query=resource

-	[bosh-deployment-resource](https://github.com/concourse/bosh-deployment-resource) - deploy bosh releases as part of your pipeline
-	[semver-resource](https://github.com/concourse/semver-resource) - automated semantic version bumping
-	[bosh-io-release-resource](https://github.com/concourse/bosh-io-release-resource) - Tracks the versions of a release on bosh.io
-	[s3-resource](https://github.com/concourse/s3-resource) - Concourse resource for interacting with AWS S3
-	[git-resource](https://github.com/concourse/git-resource) - Tracks the commits in a git repository.
-	[bosh-io-stemcell-resource](https://github.com/concourse/bosh-io-stemcell-resource) - Tracks the versions of a stemcell on bosh.io.
-	[vagrant-cloud-resource](https://github.com/concourse/vagrant-cloud-resource) - manages boxes in vagrant cloud, by provider
-	[docker-image-resource](https://github.com/concourse/docker-image-resource) - a resource for docker images
-	[archive-resource](https://github.com/concourse/archive-resource) - downloads and extracts an archive (currently tgz) from a uri
-	[github-release-resource](https://github.com/concourse/github-release-resource) - a resource for github releases
-	[tracker-resource](https://github.com/concourse/tracker-resource) - pivotal tracker output resource
-	[time-resource](https://github.com/concourse/time-resource) - a resource for triggering on an interval
-	[cf-resource](https://github.com/concourse/cf-resource) - Concourse resource for interacting with Cloud Foundry

To find out which resources are available on your target Concourse you can ask the API endpoint `/api/v1/workers`:

```
$ curl -s http://192.168.100.4:8080/api/v1/workers | jq -r ".[0].resource_types[].type" | sort
archive
bosh-deployment
bosh-io-release
bosh-io-stemcell
cf
docker-image
git
github-release
s3
semver
time
tracker
vagrant-cloud
```
