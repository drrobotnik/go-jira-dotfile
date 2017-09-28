
# go-jira
simple command line client for Atlassian's Jira service written in Go

## Install

### Download

You can download one of the pre-built binaries for **go-jira** [here](https://github.com/Netflix-Skunkworks/go-jira/releases).

### Build

You can build and install with [Go](https://golang.org/dl/):

```
go get gopkg.in/Netflix-Skunkworks/go-jira.v1/cmd/jira
```

## Configuration

**go-jira** uses a configuration hierarchy.  When loading the configuration from disk it will recursively look through
all parent directories in your current path looking for a **.jira.d** directory.  If your current directory is not
a child directory of your homedir, then your homedir will also be inspected for a **.jira.d** directory.  From all of **.jira.d** directories
discovered **go-jira** will load a **&lt;command&gt;.yml** file (ie for `jira list` it will load `.jira.d/list.yml`) then it will merge in any properties from the **config.yml** if found.  The configuration properties found in a file closests to your current working directory
will have precedence.  Properties overriden with command line options will have final precedence.

The complicated configuration hierarchy is used because **go-jira** attempts to be context aware.  For example, if you are working on a "foo" project and
you `cd` into your project workspace, wouldn't it be nice if `jira ls` automatically knew to list only issues related to the "foo" project?  Likewise when you
`cd` to the "bar" project then `jira ls` should only list issues related to "bar" project.  You can do this with by creating a configuration under your project
workspace at **./.jira.d/config.yml** that looks like:

```
project: foo
```

You will need to specify your local jira endpoint first, typically in your homedir like:

```bash
mkdir ~/.jira.d


###### **Custom Commands**
Now you can create your own custom commands to do common operations with jira.  Please see the details **Custom Commands** section below for more details.  If you want to create a command `jira mine` that lists all the issues assigned to you now you can modify your `.jira.d/config.yml` file to add a `custom-commands` section like this:
```
custom-commands:
  - name: mine
    help: display issues assigned to me
    script: |-
      {{jira}} list --query "resolution = unresolved and assignee=currentuser() ORDER BY created"
```
Then the next time you run `jira help` you will see your usage:
```
$ jira mine --help
usage: jira mine