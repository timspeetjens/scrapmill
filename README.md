# Scrapmill - a simple rpm repository manager

## Introduction
A repository url, a piece of host configuration, does not say anything about
what content the repository makes available. When package updates are released,
there is no change in configuration for the host or the build that uses
these packages. This is a challenge in some situations, like in Infrastructure
as Code and reproducible image builds, for both virtual machine images and
containers.

## Idea
So there is nothing that really describes the content of a repository, at least
not something readily available. There is something though that gets close,
being the list of RPM packages. File names aren't usable here, but we have the
package id, itself the sha256sum of the rpm file. Since all but the errata
information is extracted out of the file, this hash value is perfect to uniquely
identify the rpm file. As a bonus, this information also exists in the repodata,
since some time. If we take this value for each RPM, sort it to get a canonical
list of package identifiers, we can sha256sum that list too. That uniquely
describes our repository state. So why not use this as part of the repository
URL?

## Storing RPMs once
This is not so hard to do. A well thought of URL rewrite rule should do the
trick, as long as we don't mess up upstream repos, and upstream repo maintainers
don't mess up things, at least.

## MVP
Bold assumptions:
- Minimal error handling
- RPMs are served from `/var/www/html/repos`
- Write access in `/var/www/html`

What implementation is used to generate and expose the repositories is
irrelevant to the consumers. Agreed, a nice REST API seems appropriate, but
shell scripts suffice, for an MVP.

## Operations
The following operations can be identified, but the list is only just what's
needed and no more.

### Register an upstream
This is the equivalent of adding a repo file in /etc/yum.repos.d/. Minimal
information for the repo is needed (name, url), but to reduce the chance of
conflicts in rpm packages, also an upstream name and product is needed.

### Sync an upstream
Run reposync, with some additional steps, to generate the cannonical id, and
rename the directory.

### Serve repodata
The repo baseurl contains the id, like
`http://repo.example.com/repos/centos/centos7-base/<sha256sum>/` with
`repodata/repomd.xml` and more.

### Serve rpm packages
The Sync step will download packages in
`/var/www/html/repos/centos/centos7-base/pool/` to avoid duplicate files. With
some clever rewrite rule, we can still serve the files, from within each url
containing an id.

