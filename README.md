# Yor - Infrastructure as code tagging engine
[![Maintained by Bridgecrew.io](https://img.shields.io/badge/maintained%20by-bridgecrew.io-blueviolet)](https://bridgecrew.io/?utm_source=github&utm_medium=organic_oss&utm_campaign=yor)
![golangci-lint](https://github.com/bridgecrewio/yor/workflows/tests/badge.svg)
<a href='https://github.com/jpoles1/gopherbadger' target='_blank'>![gopherbadger-tag-do-not-edit](https://img.shields.io/badge/Go%20Coverage-82%25-brightgreen.svg?longCache=true&style=flat)</a>
[![slack-community](https://slack.bridgecrew.io/badge.svg)](https://slack.bridgecrew.io/?utm_source=github&utm_medium=organic_oss&utm_campaign=yor)

Cloud service providers allow users to assign metadata to their cloud resources in the form
of tags. Each tag is a simple label consisting of a customer-defined key and a value
that can make it easier to manage, search for, and filter resources. Although there are no
inherent types of tags, they enable customers to categorize resources by purpose, owner,
environment, or other criteria.

Tags can be used for security, cost allocation, automation, console organization, access control, and operations. 

Yor is an open-source tool that helps to manage tags in a consistent manner across infrastructure as code frameworks (Terraform, Cloudformation, Kubernetes, and Serverless Framework) .
By auto-tagging in IaC you will be able to trace any cloud resource from code to cloud. 
Yor enables version-controlled owner assignment and resource tracing based git history. It also can extend tag enforcement logic by loading external tagging logic into the CI/CD pipeline. 


## Features

* Yor collects data from [git-blame](https://git-scm.com/docs/git-blame) and enables mapping individual resources to specific commits. It can be run automatically  
* ```yor_trace``` tag enables full attribution between build time and run time resources. 
* ```git_*``` tags  connect cloud resources to individual git commits and enable assigning clear ownership between developers and the resources they routinely change.

 

### Supported tags

```
yor_trace = "842fa55e-c0f8-4f79-a56d-a046a24d8e08"
git_org = "bridgecrewio"
git_repo = "terragoat"
git_file = "README.md" # this is the path from the repo root dir...
git_commit = "47accf06f13b503f3bab06fed7860e72f7523cac" # This is the latest commit for this resource
git_last_modified_At = "2020-03-28 21:42:46 +0000 UTC"
git_last_modified_by = "schosterbarak@gmail.com"
git_modifiers = "schosterbarak/baraks" # These are extracted from the emails, everything before the @ sign. Can be done for modified_by tag as well
```



## **Table of contents**

- [Getting Started](#getting-started)
- [Disclaimer](#disclaimer)
- [Support](#support)

More Docs:
- [Customizing Yor](CUSTOMIZE.md)

## Getting Started

### Installation

On MacOS

```sh
brew install yor
```



### Usage

Yor supports the following commands:
1. `list-tag-groups` - list the groups of tags that are built into yor
   ```sh
   ./yor list-tag-groups
   ```
2. `list-tags` - list all the tags yor has built in. This will print each tag key
and the relevant group the tag belongs to.
   ```sh
    ./yor list-tags
    # List all the tags built into yor
   ./yor list-tags --tag-groups git
    # List all the tags built into yor under the tag group git
    ```
3. `tag` - apply the built in tags and any [custom](CUSTOMIZE.md) tags on a directory
   ```sh
    ./yor tag --directory terraform/
    # Apply all the tags in yor on the directory tree terraform/
   
    ./yor tag --directory terraform/ --skip-tags git_last_modified_by,yor_trace
    # Apply all the tags in yor except the tags git_last_modified_by and yor_trace
   
    ./yor tag --tag-group git --directory terraform/
    # Apply only the tags under the git tag group
    ```
   
Using docker:
```sh
docker pull bridgecrew/yor

docker run --tty --volume /local/path/to/tf:/tf bridgecrew/yor tag --directory /tf
```

Using pre-commit:

Add a hook:

```yaml
  - repo: git://github.com/bridgecrewio/yor
    rev: 0.0.44
    hooks:
      - id: yor
        name: yor
        entry: yor tag -d
        args: ["example/examplea"]
        language: golang
        types: [terraform]
        pass_filenames: false
```

To your **.pre-commit-config.yaml** and change the args and version number.

### Skipping tags

Using command line flags you can specify to run only named tags (allow list) or run all tags except 
those listed (deny list).

```sh
./yor tag -d . --skip-tags yor_trace
## Run all but yor_trace

./yor tag -d . --skip-tags yor_trace,git_modifiers
## Run all but yor_trace and git_modifiers

./yor tag -d . --skip-tags git*
## Run all tags except tags with specified patterns
```

### Skipping directories

Using the command line flag skip-paths you can define paths which won't be tagged.
Be mindful that the skipped path should include the root dir path. See example below:

```sh
./yor tag -d path/to/files
## Run on the directory path/to/files

./yor tag -d path/to/files --skip-dirs path/to/files/skip,path/to/files/another/skip2
## Run yor on the directory path/to/files, skipping path/to/files/skip/ and path/to/files/another/skip2/
```

### Output formats

```sh
./yor tag -d . -o cli
# default cli output

./yor tag -d . -o json
# json output

./yor tag -d . --output cli --output-json-file result.json
# will print cli output and additional output to file on json file -- enables prgormatic analysis alongside printing human readable result
```


#### Troubleshooting
If you encounter the following error: 
`plugin was built with a different version of package ...`

Build yor with `go build -gcflags="all=-N -l"`


## Contributing

Contribution is welcomed! 

We are working on extending Yor and adding more parsers (to support additional IaC frameworks) and more taggers (to tag using other contextual data).

To maintain our conventions, please run lint on your branch before opening a PR. To run lint:
```sh
golangci-lint run --fix --skip-dirs tests/yor_plugins
```

## Disclaimer

`yor` does not save, publish or share with anyone any identifiable customer information.  
No identifiable customer information is used to query Bridgecrew's publicly accessible guides.

## Support

If you need direct support you can contact us at info@bridgecrew.io.
