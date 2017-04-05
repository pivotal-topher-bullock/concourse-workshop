# Lab 04

## Modularize Tasks and Link Multiple Jobs

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

We will now create modularized tasks for the steps to build our application.  First create the yml task file _mvn-test.yml_.  Create the file in the _ci/tasks_ directory:

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

Create similar artifacts for the packaging stage of your application build:

_ci/tasks/mvn-package.yml_

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

outputs:
- name: app-output

run:
  path: concourse-workshop/ci/tasks/package.sh
```

_ci/tasks/package.sh_

```
#!/bin/bash

set -xe

cd concourse-workshop
mvn package
cp target/concourse-demo-*.jar ../app-output/concourse-demo.jar
```

We need to make sure our bash scripts are executable.  Execute the following command:

```
$ chmod +x ci/tasks/*.sh
```

Modify your main _pipleine.yml_, which now resides in the the ci directory, to include the 2 new tasks.  These tasks will represent completely new jobs in your pipeline.  These will test and package the java code included in your repository.  You will be replacing your "howdy" job.  Your final pipeline.yml file should look like this:

```
resources:
- name: concourse-workshop
  type: git
  source:
    branch: master
    uri: https://github.com/azwickey-pivotal/concourse-workshop

jobs:
- name: unit-test
  public: true
  plan:
  - get: concourse-workshop
    trigger: true
  - task: mvn-test
    file: concourse-workshop/ci/tasks/mvn-test.yml
- name: deploy
  public: true
  plan:
  - get: concourse-workshop
    trigger: true
    passed: [unit-test]
  - task: mvn-package
    file: concourse-workshop/ci/tasks/mvn-package.yml
```

Update your concourse pipeline with the set-pipeline command.  Remember, your pipeline.yml file is now in a different location:

```
$ fly -t lite  set-pipeline -p pipeline-<LASTNAME> -c ci/pipeline.yml
```

You'll note your pipeline now has two steps
1. Unit test your code
2. Package/Deploy your code.

Due to the `passed: [unit-test]` constraint on the `deploy` job's `get:` step, only versions of the `concourse-workshop` repo that have passed ( and succeeded ) through the `unit-test` job will continue along the pipeline.

Our last step is to check all our new pipeline code into git.  This should be everything in the ci folder.  Once we commit our build is automatically triggered:

```
$ git add ci/pipeline.yml ci/tasks

$ git commit -m "added concourse task assets"
[master 623fdb6] added concourse task assets
 3 files changed, 138 insertions(+), 2 deletions(-)
 create mode 100644 ci/pipeline.yml

$ git push
......
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (7/7), 2.04 KiB | 0 bytes/s, done.
Total 7 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local objects.
To git@github.com:azwickey-pivotal/concourse-workshop.git
   b952fc5..623fdb6  master -> master
```

![](lab04.png)
