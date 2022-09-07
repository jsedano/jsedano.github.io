---
layout: page
title: memory dump
permalink: /memory_dump/
---

This is a personal collection of things I dont't want to forget such as how I install and run things.

- [Docker](#docker)
    - [How I use it on my mid 2012 MBP](#docker_on_2012_mbp)
        - [Intallation](#install_docker_on_2012_mbp)
        - [Run / Stop](#run_docker_on_2012_mbp)
    - [postgres](#postgres_image)
        - [install](#postgres_image_install)
- [Postgres](#postgres)
    - [psql (postgres cllient)](#psql)
    - [psql commands](#psql_commands)
        - [connect](#psql_command_connect)
        - [connect to database](#psql_command_connect_to_database)
        - [run sql script](#psql_command_run_script)


## Docker <a name="docker"></a>

### How I use it on my mid 2012 MBP <a name="docker_on_2012_mbp"></a>

#### Installation <a name="install_docker_on_2012_mbp"></a>

Note: requires virtualbox (and of course homebrew installed).

{% highlight shell %}
brew install docker
brew install docker-machine
docker-machine create --driver virtualbox default
{% endhighlight shell %}


From: [https://medium.com/crowdbotics/a-complete-one-by-one-guide-to-install-docker-on-your-mac-os-using-homebrew-e818eb4cfc3](https://medium.com/crowdbotics/a-complete-one-by-one-guide-to-install-docker-on-your-mac-os-using-homebrew-e818eb4cfc3)

{% highlight shell %}
sudo mkdir /etc/vbox
sudo vi /etc/vbox/networks.conf

cat /etc/vbox/networks.conf
* 10.0.0.0/8 192.168.0.0/16
* 2001::/64
{% endhighlight shell %}

From: [https://jongsma.wordpress.com/2021/11/29/docker-and-the-virtualbox-host-only-networks/](https://jongsma.wordpress.com/2021/11/29/docker-and-the-virtualbox-host-only-networks/)

#### Run/Stop <a name="run_docker_on_2012_mbp"></a>

{% highlight shell %}
docker-machine start default
docker-machine env default
eval $(docker-machine env default)
docker-machine stop default
{% endhighlight shell %}

### postgres <a name="postgres_image"></a>

#### install <a name="postgres_image_install"></a>

{% highlight shell %}
docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d -p 5432:5432 postgres
{% endhighlight shell %}

From: [https://stackoverflow.com/a/37704532/1845671](https://stackoverflow.com/a/37704532/1845671)

## postgres <a name="postgres"></a>

### psql (postgres cllient) <a name="psql"></a>

this installs the client without the database

{% highlight shell %}
brew install libpq
echo 'export PATH="/usr/local/opt/libpq/bin:$PATH"' >> ~/.zshrc
{% endhighlight shell %}

From: [https://stackoverflow.com/a/49689589/1845671](https://stackoverflow.com/a/49689589/1845671)

### psql commands <a name="psql_commands"></a>

#### connect <a name="psql_command_connect"></a>
{% highlight shell %}
psql -h localhost -p 5432 -U postgres
{% endhighlight shell %}
#### connect to database <a name="psql_command_connect_to_database"></a>
{% highlight shell %}
psql -h localhost -p 5432 -U postgres -d demo
{% endhighlight shell %}
#### run sql script <a name="psql_command_run_script"></a>
{% highlight shell %}
psql -h localhost -p 5432 -U postgres -f demo-small-en-20170815.sql
{% endhighlight shell %}
