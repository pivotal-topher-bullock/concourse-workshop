# Prerequisites For Labs

## Install FLY CLI

In a browser navigate to https://wings.concourse.ci and select the icon
associated with your platform:

![](fly.png)

Add the downloaded binary to your system path

After adding to your system path you should be able to execute the fly command
from a terminal window:

`fly --version`

## Target and Log In to Concourse Env

Execute the fly target command to connect to the Concourse environment, and
replace `{team-name}` with the team name provided by your instructor. When
prompted to enter a username and password enter the credentials provided to you
by your instructor.

```
fly --target demo login --concourse-url http://wings.concourse.ci --team-name {team-name}
```

## Create a GIT repository to hold your concourse pipeline and artifacts

Fork this git repository:  https://github.com/pivotal-topher-bullock/concourse-workshop

Clone this repository to your local desktop

```
$ git clone <YOUR-GIT-URL>
```

In your terminal window change directories to the root of your cloned git repo.
We'll use this location as a base for most our commands in the workshop
