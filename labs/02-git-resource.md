# Lab 02

## The Git Resource

The git resource is one of the built-in resources that ships with any release of Concourse. The 'in' command for this resource pulls down a git repository at a given commit ref, while the 'out' command can push new commits up to the repository.

Modify the file pipeline.yml that you previously created.  Add the following text, which refers to a Git repository to the top of your file:

```yaml
resources:
- name: git-assets
  type: git
  source:
    branch: master
    uri: https://github.com/pivotal-topher-bullock/concourse-workshop
```

You'll notice we can specify a `type` some `source:` params for a resource


Modify the plan of the "hello-world" job in the jobs section of the file to add a new step that will `get` the git repository:

```yaml
jobs:
- name: hello-world
  plan:
  - get: git-assets   # Lines to add
    trigger: true     # Lines to add
  - task: say-hello
    config:
      platform: linux
      image_resource:
        type: docker-image
        source: {repository: ubuntu}
      run:
        path: echo
        args: ["Hello, world!"]
```

 Your full pipeline file should look like this:

```yaml
resources:
- name: concourse-workshop
  type: git
  source:
    branch: master
    uri: https://github.com/pivotal-topher-bullock/concourse-workshop

jobs:
- name: hello-world
  plan:
  - get: concourse-workshop
    trigger: true
  - task: say-hello
    config:
      platform: linux
      image_resource:
        type: docker-image
        source: {repository: ubuntu}
      run:
        path: echo
        args: ["Hello, world!"]
```

Update your pipeline in on the concourse server with the set-pipeline command.

```$ fly -t lite  set-pipeline -p hello-world -c pipeline.yml```

You'll note that when you update a pipeline the fly CLI response will highlight what has changed in the pipeline.

Return back to the Concourse Web UI.  You'll note your pipeline has now changed to include the git-assets resource, which is an input to your pipeline:


![](resource.png)

Your pipeline will now trigger automatically anytime the git repository changes.  This is because we marked the _get_ task in the pipeline to be triggered.  By clicking on the hello-world job you should now see the output of both a git clone and execution of the task that outputs a simple message to STDOUT:

![](lab03-execute.png)
