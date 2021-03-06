[![Build Status](https://snap-ci.com/ashwanthkumar/gocd-build-github-pull-requests/branch/master/build_image)](https://snap-ci.com/ashwanthkumar/gocd-build-github-pull-requests/branch/master)

# GoCD - Git feature branch support
This is a GoCD SCM plugin for Git feature branch support. [Discussion Thread](https://groups.google.com/d/topic/go-cd-dev/Rt_Y5G2VkOc/discussion)

Supported (as seperate plugins):
* Git repository for branches
* Github repository for Pull Requests
* Stash repository for Pull Requests
* Gerrit repository for Change Sets

## Requirements
This needs GoCD >= v15.x which is due release as of writing.

## Get Started
**Installation:**
- Download the latest plugin jar from [Releases](https://github.com/ashwanthkumar/gocd-build-github-pull-requests/releases) section. Place it in `<go-server-location>/plugins/external` & restart Go Server.

**Usage:**
- Assuming you already have a pipeline "ProjectA" for one of your repos, 'Extract Template' from the pipeline (if its not templatized already)
- Create new pipeline say "ProjectA-FeatureBranch" off of the extracted template (you can clone "ProjectA" pipeline to achieve this)
- Select `Git Feature Branch`/`Github`/`Stash`/`Gerrit` in Admin -> 'Pipeline' Configuration (ProjectA-FeatureBranch) -> 'Materials' Configuration -> 'Material' listing drop-down

### Github

**Authentication:**
- You can create a file `~/.github` with the following contents: (Note: `~/.github` needs to be available on Go Server and on all Go Agent machines)
```
login=johndoe
password=thisaintapassword
```

- You can also generate & use oauth token. To do so create a file `~/.github` with the following contents: (Note: `~/.github` needs to be available on Go Server and on all Go Agent machines)
```
login=johndoe
oauth=thisaintatoken
```

**Github Enterprise:**
- If you intend to use this plugin with 'Github Enterprise' then add the following content in `~/.github` (Note: `~/.github` needs to be available on Go Server and on all Go Agent machines)
```
# for enterprise installations - Make sure to add /api/v3 to the hostname
# ignore this field or have the value to https://api.github.com
endpoint=http://code.yourcompany.com/api/v3
```

## Behavior
- First run of the new pipeline will be off of 'master' branch. This creates base PR-Revision map. It also serves as sanity check for newly created pipeline.
- From then on, any new change (new PR create / new commits to existing PR) will trigger the new pipeline. Only the top commit in the PR will show up in build cause.
- PR details (id, author etc.) will be available as environement variable for tasks to consume.

You can use [GoCD build status notifier](https://github.com/srinivasupadhya/gocd-build-status-notifier) to update status of Pull Requests with build status.

## To Dos
- Clean up the code esp. the JSON SerDe part
- Add proper tests around the plugin

## FAQs

### Pull Request isn't being built
- If more than 1 PR gets updated (create/update) Go bunches them together for the next pipeline run & uses the top change in the "build-cause" to build. You can force trigger pipeline with other revisions until this get fixed ([thread](https://github.com/gocd/gocd/issues/938)).

