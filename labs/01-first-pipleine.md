# Lab 01

## Vagrant Up for Local Development

Concourse can be [installed in many different flavours](http://concourse.ci/installing.html), depending on your needs and the scale of your team. For our purposes, we'll be using a local VM with Vagrant; this is the easiest way to get up and running in 5 minutes.

Run the following in a new directory from your terminal.

```
$ vagrant init concourse/lite  # creates ./Vagrantfile
$ vagrant up                   # downloads the box and spins up the VM
```

The Concourse web server will be running at 192.168.100.4:8080. From that page you can download the `Fly` cli to log into the Concourse server and start making pipelines!

`fly -t lite login -c http://192.168.100.4:8080`

## 1st Concourse Pipeline

Create the file hello.yml using your preferred text editor or IDE.  In this step we are creating a job within a pipeline, rather than running a one-off task:

``` yaml
jobs:
- name: hello-world
  plan:
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

To deploy this pipeline to the concourse server use the fly command to set a pipeline.

```$ fly -t lite set-pipeline -p hello-world -c hello.yml```

You'll note the command informs us that initially our pipeline is in a paused state.  We could un-pause from the command line, but instead we'll un-pause and execute the pipeline from the Concourse web UI.

Select the "main" team to login to and use the credentials previously provided to authenticate

![Selecting a team to log in](login.png)

Expand the list of pipelines by clicking the menu icon in the upper left.  You should see you pipeline listed with a blue play button.  Click that button to unpause your pipeline:


![unpausing the pipeline](play.png)

Lastly, click on your pipeline and then click on and large box that says "hello" in the center of the screen.  Hello is the name of your job.  After selecting your job click the + sign in the upper right to kickoff the job.

![triggering a build](execute-lab01.png)
