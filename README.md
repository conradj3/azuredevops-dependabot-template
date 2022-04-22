# Dependabot Azure DevOps Extension

This is the unofficial dependabot extension for Azure DevOps. It will allow you to run Dependabot inside a build pipeline. You will find it here in the Visual Studio marketplace. You need to install it before running dependabot in your pipeline.


## Template Package-Ecosystem Configuration

The follow template supports Npm, Yarn, Pip, Docker, Terraform.  There are many more environments this extension can support.  For more details see the official [GitHub Docs - package-ecossytem](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file#package-ecosystem)

## Template Parameters

By default, all tasks will be skiped if the `enable*` parameters are not called with the template.  Additional, the user may choose to substitute the default root path per each package-ecosystem. 

```yaml
# Node.Js Parameter Configuration
- name: enableNpm
  type: boolean
  default: false

- name: npmDirectory
  type: string
  default: '/'

# Docker Parameter Configuration
- name: enableDocker
  type: boolean
  default: false

- name: dockerDirectory
  type: string
  default: '/'

# NuGet Parameter Configuration
- name: enableNuget
  type: boolean
  default: false

- name: nugetDirectory
  type: string
  default: '/'

# Terraform Parameter Configuration
- name: enableTerraform
  type: boolean
  default: false

- name: terraformrDirectory
  type: string
  default: '/'
 
 # Pip Parameter Configuration
- name: enablePip
  type: boolean
  default: false

- name: piprDirectory
  type: string
```

### Work Item Parameters

```yaml
  # WorkItem Parameters
  - name: areaPath
    type: string
    default: '<DEFAULT AREA PATTH>'
  
  - name: iterationPath
    type: string
    default: '<DEFAULT ITERATION PATTH>'
```

## Usage of the Dependabot Template

Place the following file in the  `.azuredevops` folder root, and configure as desired.  A pipeline will need to be generated with a proper name `<repository>-dependabot`, ensure you place this pipeline in the correct folder management system. You may choose to run it to test its functionality or wait for the cron trigger.

***dependabot.yaml***
```yaml
# Crontab Triggers for weekly run.
# Run will trigger at 04:00 AM on Monday
# Support for crontab can be found here https://crontab.guru/#0_4_*_*_1
schedules:
  - cron: "0 4 * * 1"
    displayName: 'Weekly Run'
    always: true
    branches:
      include:
        - main

# Pipeline Trigger is set to none as we will use cron to trigger weekly updates.
trigger: none

# Dependecy Repository for Dependabot Template.
resources:
  repositories:
    - repository: "<REPOSITORY_NAME>"
      type: git
      name: "<PROJECT/REPOSITORY>"
      ref: refs/heads/master

# Primary Stage for calling Dependabot template.
stages:

# By default all runs are disabled, and must be selected by the team.
# Parameter Explaiantions
#    enableNpm: <bool>        <-- Supports Npm/Yarn
#    enableDocker: <bool>     <-- Official DockerHub Images not our Azure Container Registry.
#    enableNuget: <bool>      <-- Supports .Net NuGet
#    enableTerraform: <bool>  <-- Supports Terraform Providers and Modules
#    enablePip: <bool>        <-- Supports Python Pip / Pip3

- template: dependabot/dependabot-template.yaml@DevOps_Templates
  parameters:
    enableNpm: true 
    npmDirectory: '/'            # Defaulted by shown for usability.
    enableDocker: false          # Defaulted by shown for usability.
    dockerDirectory: '/'         # Defaulted by shown for usability.
    enableNuget: false           # Defaulted by shown for usability.
    nugetDirectory: '/'          # Defaulted by shown for usability.
    enableTerraform: false       # Defaulted by shown for usability.
    terraformDirectory: '/'      # Defaulted by shown for usability.
    enablePip: false             # Defaulted by shown for usability.
```

## Agents and Authentication

It is by design that these jobs use the Azure Hosted Agent Ubuntu Pool.  However, this can be modified to run on self-hosted pools.

Authentication to AzureDevOps API is enabled by `$(System.AccessToken)` which curerntly uses the Project Collections Build Service account.  The task also makes use a GitHub personal access token that has `public:repo` access for authentication to get by the GitHub API Limiter for dependency checks.


## Reference Links

- [tinglesoftware -dependabot-azure-devops](https://github.com/tinglesoftware/dependabot-azure-devops)
- [AzureDevOps - Marketplace](https://marketplace.visualstudio.com/items?itemName=tingle-software.dependabot)
- [GitHub Docs - Dependabot](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file#about-the-dependabotyml-file)