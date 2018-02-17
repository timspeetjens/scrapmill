# Howto

## Requirements
- An rpm based linux system
- `reposysnc` and `httpd` packages installed
- A user that can write inside `/var/www/html` directory

## Configure a rewrite rule for apache
Copy `etc/20-rewrite.conf` into `/etc/httpd/conf.modules.d` and restart apache.

## Setup repos
Run `scrapmill-register <vendor> <reponame> <url>` to define the repository.

## Sync repos
Run `scrapmill-register <vendor> <reponame>`.

## Use the repos
This is as simple as using `http://host/repos/<vendor>/<reponame>/<repohash>` as
the baseurl for yum/dnf.

Find the repohash as the directory created in `/var/www/html/repos/<vendor>/<reponame>/`.

## Check the repo content
A simple textual diff can be run between the `scrapmeta` files found in
different versions of the repository, i.e. directories named as sha256sums.

