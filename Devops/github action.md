
# components

**Workflow**  = the automation pipeline. /Defined in YAML files stored under .github/workflows/ . similiar to pipeline in jenkins

**Event** = The trigger that starts a workflow.
- push -> triggered when code is pushed
- pull_request -> triggered on PR creation/update 
- schedule -> cron jobs
- workflow_dispatch -> manual trigger
in jenkins, its like webhook


**Job** = collection of steps. A set of steps that run in the same runner(vm)
Example: build, test, deploy jobs.
jenkins -> stages

**step** = command or action. A single task in a job. a commnd/script

Jenkins -> its like steps

**Action** = reusable component
example: actions/echeckout@v4 -> checks out repo code.
jenkins -> its like plugins

**Runner** = machine executing jobs/ Github-hosted runner -> ubuntu, windows, macos

**self-hosted runners** -> your own machine/server /vm
same like jenkins slaves

**Environment** = Env is a named deployment target (dev, staging, production)

useful for controlling where and how workflows deploy

**Secrets** = Secrets store sensitive values(passwords, tokens, api keys) securly in github
- Repository secrets = available to one repo
- Environment secrets = scoped to specific environment
- Organization secrets = share across multiple repos

**Cache** = the cache in github actions helps speed up workflows by storing dependencies or build outputs between workflow runs
