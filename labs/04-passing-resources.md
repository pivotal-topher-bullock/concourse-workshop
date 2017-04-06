# Lab 03

## Passing Resources Between Tasks

Lets modify `pipeline.yml` to add another job that triggers on versions of `concourse-workshop` that pass through the first job.

```yaml
- name: deploy
  plan:
  - get: concourse-workshop
    trigger: true
    passed: [unit-test]
  - task: mvn-package
    file: concourse-workshop/ci/tasks/mvn-package.yml```

Update your concourse pipeline with the set-pipeline command.

```
$ fly -t lite set-pipeline -p pipeline -c ci/pipeline.yml
```

You'll note your pipeline now has two steps
1. Unit test your code
2. Package/Deploy your code.

Due to the `passed: [unit-test]` constraint on the `deploy` job's `get:` step, only versions of the `concourse-workshop` repo that have passed ( and succeeded ) through the `unit-test` job will continue along the pipeline.

### Add task files 

As we did in the previous step, we'll need to create tasks for the packaging stage of your application build:

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

Again, we need to make sure our bash scripts are executable. And run `$ chmod +x ci/tasks/*.sh`

