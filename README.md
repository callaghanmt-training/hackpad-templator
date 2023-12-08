# Research Computing Training Templater

This repository contains the code for a github action workflow that automatically generates a Hackmd hackpad for 
our training workshops using the hackmd-cli and jinja2 templates.

## Workflow

```
Create a new branch in this repo
containing a metadata file
            |
            |
            v
Github action detects the new branch and starts the action
            |  
            |
            v
Github action reads metadata file and uses variables to fill a jinja template
            |
            |
            v
This markdown file is then passed to hackmd-cli which imports the .md file as a new hackpad
```

## Usage

You can use this repository to automatically create a new Hackmd.io hackpad for Research Computing training sessions using templates included within this repo.

To create a new hackpad you need to create a branch with the format `<training-course-code>-yyyy-mm` for example for [HPC0](https://arc.leeds.ac.uk/training/courses/hpc0/) running in November 2021 you would create a branch `hpc0-2021-11`. You should then create a `config.yaml` file at the root of your repository containing values to be autofilled for the [template](#expected-config).

**JinJa is picky over the file ending of the config file. Ensure it .yaml rather than .yml!**

```bash
# example git command for creating a new branch
$ git checkout -b hpc0-2021-11

# create a config.yaml in the repo root
$ cat <<EOF > config.yaml
date: 2021-11-22
morning: true
EOF

# add stage config file for commit
$ git add config.yaml

# commit changes
$ git commit -m 'adding config file for workshop'

# push branch back to github to trigger action
$ git push -u origin hpc0-2021-11

```

**After the hackpad is created you will still need to configure the hackpads sharing settings and edit the url slug of the hackpad which can be done via the hackmd.io interface.**

## Expected `config`

Each template expects a `config.yaml` file to be committed within the branch to generate the hackpad. These just have to be simple
yaml documents that specify the variables that jinja2 will fill in.

### HPC0 & HPC1

Expected yaml file:

```yaml
date: "2021-11" # specify the date to be included in the hackmd heading
morning: True # specify if the session is morning or not to control generated agenda timings
```

### SWD6

Expected yaml file:

```yaml
date: "2021-11" # specify the date to be included in the hackmd heading
```

## Changing files in this repository

If changing the template where you don't want to test that it deploys correctly, it is important to name the branch to something starting "dev" e.g. "dev-hpc0-user-0". This is because of the way the GitHub actions are configured. Failure to use a name starting with "dev" will result in it trying to deploy, so normal branch naming rules apply, and it will fail if it's not an appropriate name. Renaming the branch does not seem to resolve this, so take care to name the branch correctly first time.

## Notes on required secrets

There are two secrets GitHub requires in the webhook.  These are set under settings, Secrets and variables, Actions.

*MS\_TEAMS\_WEBHOOK\_URI*

This can be generated by going to your chosen Teams channel, right clicking,
selecting connectors, Incoming Webook, configure.  Give it a name, create it,
and you get a URL that you need.

*HMD\_API\_ACCESS\_TOKEN*

Go to Hackmd, login, select your profile in the bottom left, settings, API,
Create API token.  That gives you the credential you need.
