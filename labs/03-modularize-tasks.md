# Lab 04

## Modularize Tasks

It is very common within an application's git repository to place all concourse artifacts in a _ci_ folder.  Within that folder individual tasks are modularized in their own yml files and refer to shell scripts for actual task execution.  E.G.

```
App Git Repo Root:
├── App source code....
│   ├── Some code artifacts...
├── ci
│   ├── pipeline.yml
│   ├── credentials.yml
│   ├── tasks
│   │   ├── modular-task.yml
│   │   ├── modular-task.sh
```

We will mimic that structure as we build out our pipeline

Create the _ci_ and _ci/tasks_ directory structure:

`$ mkdir -p ci/tasks`

Move the pipeline.yml file into your new _ci_ directory

`$ mv pipeline.yml ci/pipeline.yml`

We will now create a modularized task files for the steps to test our application.  First create the yml task file _mvn-test.yml_.  Create the file in the _ci/tasks_ directory:

```
---
platform: linux

image_resource:
  type: docker-image
  source:
    repository: jamesdbloom/docker-java8-maven
    tag: "latest"

inputs:
- name: concourse-workshop

run:
  path: concourse-workshop/ci/tasks/test.sh
```

You'll note that this task references to script _test.sh_ for the actual execution logic.  Create that file:

```
#!/bin/bash

set -xe

cd concourse-workshop
mvn test
```

We need to make sure our bash scripts are executable by running `$ chmod +x ci/tasks/*.sh`

Modify your main _pipleine.yml_, which now resides in the the ci directory. Let's replace our `hello-world` job with one named `unit-test` that pulls in our git repo, and then runs the `mvn-test` task; this will test the java code included in your repository.

Your final pipeline.yml file should look like this:

```
resources:
- name: concourse-workshop
  type: git
  source:
    branch: master
    uri: https://github.com/pivotal-topher-bullock/concourse-workshop

jobs:
- name: unit-test
  public: true
  plan:
  - get: concourse-workshop
    trigger: true
  - task: mvn-test
    file: concourse-workshop/ci/tasks/mvn-test.yml
```

Our last step is to check all our new pipeline code into git.  This should be everything in the ci folder.  Once we commit our build is automatically triggered:

```
$ git add ci/pipeline.yml ci/tasks
$ git commit -m "added concourse task assets"
$ git push
```

![](lab04.png)
