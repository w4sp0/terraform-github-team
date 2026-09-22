<!--
SPDX-FileCopyrightText: 2020-2022 Mineiros GmbH <hello@mineiros.io>
SPDX-FileCopyrightText: 2025 Radek Janik <cyberwassp@gmail.com>

SPDX-License-Identifier: Apache-2.0
-->

# terraform-github-team

[![Terraform Version](https://img.shields.io/badge/terraform-1.x-623CE4.svg?logo=terraform)](https://github.com/hashicorp/terraform/releases)
[![Github Provider Version](https://img.shields.io/badge/GH-6.x-F8991D.svg?logo=terraform)](https://github.com/integrations/terraform-provider-github/releases)

## Fork Notice

This is a maintained fork of [mineiros-io/terraform-github-team](https://github.com/mineiros-io/terraform-github-team), which is no longer actively maintained. The goal of this fork is to bring the module up to GitHub Provider v6 compatibility and continue ongoing maintenance.

**Attention**: This module requires the `integrations/github` provider and is incompatible with the deprecated `hashicorp/github` provider.

## Warning

**Warning**: As this repository is being brough up to GitHub provider v6, it is
currently not ready for production, development only. Breaking changes can and
will be introduced in the meantime. You've been warned.



## Table of Contents

- [Description](#description)
- [Features](#features)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Module Reference](#module-reference)
- [Contribute](#contribute)
- [Support](#support)
- [Contact](#contact)
- [Credits](#credits)
- [Legal](#legal)
- [Versioning](#versioning)

## Description

A [Terraform](https://www.terraform.io) module for creating and managing [GitHub Teams](https://docs.github.com/en/organizations/organizing-members-into-teams/about-teams) including memberships and repository access.

This module supports Terraform v1.x and is compatible with the Official Terraform GitHub Provider v6.x from `integrations/github`.

Originally developed by [Mineiros](https://github.com/mineiros-io), this fork aims to:

- Update compatibility to GitHub Provider v6
- Fix outstanding issues and bugs
- Continue active maintenance and development

In contrast to using individual GitHub provider resources, this module provides a convenient wrapper that manages teams, nested teams, memberships, and repository permissions in a cohesive and tested way.

## Features

- **Default Security Settings**: Secret team visibility by default
- **Standard Team Features**: Team creation, description, privacy settings, nested teams, LDAP sync
- **Extended Team Features**: Team memberships (members and maintainers), repository access with granular permissions (admin, maintain, push, triage, pull)

## Getting Started

Most basic usage creating a GitHub team:

```hcl
module "team" {
  source  = "w4sp0/team/github"
  version = "~> 0.9.0"

  name        = "DevOps"
  description = "The DevOps Team"
  privacy     = "secret"

  members     = ["developer-1", "developer-2"]
  maintainers = ["team-lead"]
}
```

## Usage

See [variables.tf](variables.tf) and [examples/](examples/) for detailed use-cases.

The intended behavior is to enforce the state of teams and their configurations. If you modify resources outside of Terraform and reapply the state, conflicting configurations will be overwritten.

For comprehensive documentation on module arguments, outputs, and advanced configurations, see [MODULE_REFERENCE.md](docs/MODULE_REFERENCE.md).

### Quick Reference

| Argument | Description | Default |
|----------|-------------|---------|
| `name` | Team name | Required |
| `description` | Team description | `""` |
| `privacy` | `secret` or `closed` | `"secret"` |
| `parent_team_id` | Parent team ID for nested teams | `null` |
| `create_default_maintainer` | Add creating user as maintainer | `false` |
| `members` | List of member usernames | `[]` |
| `maintainers` | List of maintainer usernames | `[]` |
| `admin_repositories` | Repositories with admin access | `[]` |
| `maintain_repositories` | Repositories with maintain access | `[]` |
| `push_repositories` | Repositories with push access | `[]` |
| `triage_repositories` | Repositories with triage access | `[]` |
| `pull_repositories` | Repositories with pull access | `[]` |

### Team with Repository Access

```hcl
module "team" {
  source = "w4sp0/team/github"

  name        = "Backend Team"
  description = "Backend development team"
  privacy     = "closed"

  # Team members
  members = [
    "developer-1",
    "developer-2",
  ]

  # Team maintainers
  maintainers = [
    "tech-lead",
  ]

  # Repository permissions
  admin_repositories = [
    "backend-core",
  ]

  push_repositories = [
    "backend-api",
    "backend-services",
  ]

  pull_repositories = [
    "frontend-app",
    "shared-libs",
  ]
}
```

### Nested Teams

```hcl
module "engineering" {
  source = "w4sp0/team/github"

  name        = "Engineering"
  description = "All engineering staff"
  privacy     = "closed"
}

module "backend" {
  source = "w4sp0/team/github"

  name           = "Backend"
  description    = "Backend engineering team"
  privacy        = "closed"
  parent_team_id = module.engineering.id

  members     = ["backend-dev-1", "backend-dev-2"]
  maintainers = ["backend-lead"]
}

module "frontend" {
  source = "w4sp0/team/github"

  name           = "Frontend"
  description    = "Frontend engineering team"
  privacy        = "closed"
  parent_team_id = module.engineering.id

  members     = ["frontend-dev-1", "frontend-dev-2"]
  maintainers = ["frontend-lead"]
}
```

## Module Reference

For the complete module argument reference, outputs, and advanced configurations, see [docs/MODULE_REFERENCE.md](docs/MODULE_REFERENCE.md).

### External Documentation

- [Terraform GitHub Provider - Team](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/team)
- [Terraform GitHub Provider - Team Membership](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/team_membership)
- [Terraform GitHub Provider - Team Repository](https://registry.terraform.io/providers/integrations/github/latest/docs/resources/team_repository)

## Contribute

Contributions are welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for contribution guidelines.

Areas where help is especially appreciated:

- Testing with GitHub Provider v6
- Documentation improvements
- Bug fixes and feature additions

## Support

Support is provided on a best effort basis. If you encounter issues or have feature requests, please open an issue on GitHub. The project is best developed in the open so anyone can search for past issues and solutions.

## Contact

- [GitHub Issues](../../issues) - Bug reports and feature requests
- [GitHub Discussions](../../discussions) - Questions and community discussion

## Credits

This module was originally developed by [Mineiros GmbH](https://mineiros.io). Their work on Terraform modules for GitHub management laid the foundation for this project.

Thanks to all contributors to both the original repository and this fork.

## Legal

This project is [REUSE-compliant](https://reuse.software).

The easiest way to get the copyright and license of the project is with the reuse tool:

```sh
reuse spdx
```

You can also check this information manually by looking in the file header, a companion `.license` file, or in `.reuse/dep5`.

All licenses are present in the `LICENSES` directory.

## Versioning

This project follows [Semantic Versioning (SemVer)](https://semver.org/):

- `MAJOR` version for incompatible changes
- `MINOR` version for backwards compatible functionality
- `PATCH` version for backwards compatible bug fixes
