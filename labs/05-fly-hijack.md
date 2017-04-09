# Lab 05

## SSH into a Concourse task container for debugging

Builds don't always run smoothly.  At times a developer or operator will need to
debug a failing build.  Concourse provides the ability to SSH into a pipeline
task in order to allow for troubleshooting and debugging.  You may do this using
the `fly hijack` CLI command.  The format is
`fly hijack {pipeline-name}/{job-name}`

SSH into one of your build tasks.  Replace `{your-name}` with the name you used
when naming your pipeline.  If you are presented with multiple tasks to log into 
choose the most recent build.

`$ fly -t demo  hijack -j ci-{your-name}/deploy`

Once you are connected to the container you can navigate the file system and
execute commands.  Execute a `mvn clean package` on the code that is cloned from
git:

```
root@bd618476-54f3-4b7b-7403-8002b34d07c5:/tmp/build/f5866da1# cd git-assets/
root@bd618476-54f3-4b7b-7403-8002b34d07c5:/tmp/build/f5866da1/git-assets# mvn clean package
```
