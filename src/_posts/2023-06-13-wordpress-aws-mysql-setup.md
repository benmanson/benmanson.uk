---
layout: posts
title:  "Setting up Wordpress on AWS."
date:   2023-06-13 13:31:11 +0100
# categories: aws wordpress php mysql ubuntu linux apache2 apache
---
Often, plugins in Wordpress or guides explaining how to set up new funcitonality will fall back on something like, 'Please ask your hosting provider to set this up for you', or 'Have your system administrator configure'. This guide aims to instruct the user how to set up and manage the hosting themselves using AWS, and potentially resolve some of these problems where using a hosting provider is outwith your means. Alternatively, if you *are* a hosting provider this guide may be useful where you have only ever run into the dreaded 'Ask your hosting provider' "solution" to something you are trying to resolve - speaking from experience!

## General notes

Commonly, Wordpress is hosted on top of a [LAMP](https://en.wikipedia.org/wiki/LAMP_(software_bundle)) stack. At the very least, the 'P' is going to be a requirement. You can also use [W(indows), N(ginx)](https://winnmp.wtriple.com/tutorial/wordpress), [P(ostgres)](https://www.enterprisedb.com/postgres-tutorials/how-deploy-wordpress-highly-available-postgresql) optionally, but it's going to be far easier - and better supported - to just stick to LAMP (as this guide will).

Many existing guides focus on setting up using the following particular versions of LAMP:

- Linux: **Ubuntu 20.04**
- MySQL: **5.7**
- PHP: **7.4**

Please refer to [this site](https://endoflife.date/) for updated information on end-of-life for these, but as of present [MySQL 5.7](https://endoflife.date/mysql) EOL for extended support is 4 months away, with premier support having ended 2 years ago. [PHP 7.4](https://endoflife.date/php) is in a similar position, with EOL having passed for both active and security support. Wordpress still only has 'beta' support for [PHP 8.0+](https://make.wordpress.org/core/handbook/references/php-compatibility-and-wordpress-versions/), but note that this guide is going to focus on using PHP 8.2 and MySQL 8.0 anyway.

## Infrastructure

Technically, all you need to host a Wordpress server is a server machine and a domain to put it behind. In smaller use cases, this is going to be more than enough in order to host something like a personal blog.

### VPC

While generally I would opt to keep instances within a private subnet - and this is technically possible with extra cost, or more manual effort - it is much easier to manage plugins, themes etc if the server is within a public subnet and has access to the internet.

### IAM/SSM

If you need to copy any files onto your server, you can do this using SSH. However, the [recommended](https://aws.amazon.com/blogs/mt/vr-beneficios-session-manager/) method of connecting securely to your instance is using AWS Session Manager.

You can setup SSH over Session Manager, however I generally find that to be a bit of a [faff](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started-enable-ssh-connections.html). When required, I simply use an SSH security group to allow access from my IP only, copy the file using `scp`, and then remove this security group association.

Newly launched EC2 instances will automatically install and configure the SSM agent to allow session manager to be used if they have the IAM policy `AmazonSSMManagedInstanceCore` attached to them.

Navigate to the IAM console and select 'Roles' in the left sidebar:

![iam console](/assets/images/2023/06/13/iam-roles.png)

Select 'Create role' on the console. On the first page, use the following options:

- Trusted entity type: **AWS service**
- Use case: **EC2**

Under the 'Add permissions' panel, search for the previously stated role - `AmazonSSMManagedInstanceCore`. We might require other permissions later, but for now this is the only one that we need. Name the policy something descriptive i.e. `WordpressManagedInstanceRole`, and create it.

### EC2

In the AWS console, navigate to the EC2 home.

## Server configuration

### Install dependencies

I like to use the repository by [Ondřej Surý](https://launchpad.net/~ondrej/+archive/ubuntu/php/) to install and maintain PHP. This repository contains most PHP releases, and so upgrading or changing versions is much easier than using the default repositories that come with the EC2. You can install this repository using the following two commands:

{% highlight bash %}
sudo add-apt-repository ppa:ondrej/php
sudo apt update
{% endhighlight %}

### Configuring PHP

### Configuring MySQL

## Making your site more 'production' ready

### RDS

<!-- [jekyll-docs]: https://jekyllrb.com/docs/home -->
<!-- [jekyll-gh]:   https://github.com/jekyll/jekyll -->
<!-- [jekyll-talk]: https://talk.jekyllrb.com/ -->
