Test Driven Infrastructure with Chef
====================================

An introduction to automated infrastructure and how to apply test driven techniques to
infrastructure, by Stephen Nelson-Smith.

## The Philosophy of Test-Driven Infrastructure

Two fundamental rules:

1. Infrastructure can and should be treated as code.
2. Infrastructure developers should adhere to the same principles of professionalism as other
   software developers.

Treating infrastructure as code means: repeatability, automation, agility, scalability, reassurance,
and disaster recovery.

Infrastructure code should strive for the same good qualities as application code: modularity,
composability, extensibility, flexibility, repeatability, declaration, abstraction, and idempotence.

And you'll need to perform the same actions as you do with application code: design, collective
ownership, code reviews, code standards, refactoring, and testing.

## An Introduction to Chef

See <http://www.opscode.com/chef/> for latest install instructions or run:

    curl -L https://www.opscode.com/chef/install.sh | sudo bash

This will install the programs:

* `Ohai` - system profiling
* `chef-apply` - lightweight config tool to perform single command
* `chef-shell` - an interactive REPL
* `chef-solo` - standalone config management tool to be used without the server, for simple deploys
* `chef-client` - agent that runs on systems managed by Chef to communicate with the server
* `knife` - multipurpose tool to faciliate system automation, deployment, and integration

Chef is a framework, tool, and API:

* framework - provides libraries for managing almost every resource there is
* tool - see the program list above for more details about installed tools
* API - Chef is a client/server web services that is driven via a RESTful API (knife is the client)

**Chef is simply an API, an index, and a dependency resolver.**

A huge advantage of using Chef is the community that supports it.  There are pre-existing recipes
that can do the heavy lifting for you.

### Use Chef to Install User on Local Machine via `chef-apply`

1. Create `tdi.rb` file:
2. Read docs for user resource: <http://docs.opscode.com/chef/resources.html#user>
3. Declare a resource in `tdi.rb` to create user `tdi`

    user 'tdi' do
      action :create
      comment 'Test Driven Infrastructure'
      home '/home/tdi'
      supports manage_home: true
    end

4. Run `sudo chef-apply tdi.rb` to create user on local machine
5. Verify user installed with `getent passwd | grep tdi`
6. Add another resource `dotfile` to drop off `.tdi` file

    dotfile '/home/tdi/.tdi' do
      action :create
      content 'bogus'
    end

7. Run `chef-apply` again
8. Observe failures
9. Replace resource `dotfile` with `file` and run `chef-apply` again
10. Replace `file` resource with `template` resource

    template '/home/tdi/.tdi' do
      action :create
      source 'tdi-bashfile'
    end

11. Run `chef-apply`

### Use Chef to Install IRC Client via `chef-solo`, recipes, cookbooks, and run lists

1. Run `chef-solo` without any configuration, what's the output?

    ...
    FATAL: Chef::Exceptions::CookbookNotFound: No cookbook found in ["/var/chef/cookbooks", ...],
    make sure cookbook_path is set correctly
    ...

2. Run `knife help cookbook` and create cookbook `irc`

    $ knife cookbook create irc -o .
    $ ls -1F irc/

3. Read the documentation at: <http://docs.opscode.com/resource_package.html>,
   <http://docs.opscode.com/resource_cookbook_file.html>,
   <http://docs.opscode.com/resource_directory.html>
4. Open `irc/recipes/default.rb` and replace it with user resource

    user 'tdi' do
      action :create
      comment "Test Driven Infrastructure"
      home "/home/tdi"
      manage_home true
    end

5. Add resource to install `irssi` package

    package 'irssi' do
      action :install
    end

6. Add resource to create `.irssi` directory in `tdi` user's home directory

    directory '/home/tdi/.irssi' do
      owner 'tdi'
      group 'tdi'
    end

7. Add resource for `~/.irssi/config`

    cookbook_file '/home/tdi/.irssi/config' do
      source 'irssi-config'
      owner 'tdi'
      group 'tdi'
    end

    # files/default/irssi-config
    servers = (
      {
        address = "irc.freenode.net";
        chatnet = "Freenode";
        ...
      }
    )
    ...

8. Search docs site for `run list`: an ordered list of roles/recipes to run in an exact order
9. Create a `solo.rb` config file and specify cookbook path

    $ mkdir ~/.chef
    $ echo "cookbook_path ENV['HOME']" > ~/.chef/solo.rb

10. Run `chef-solo` to converge the node with default recipe from irc cookbook

    $ sudo chef-solo --config ~/.chef/solo.rb --override-runlist 'recipe[irc]'

11. Login as "tdi" and launch your IRC client

We created an IRC recipe in `irc/recipes/default.rb`, which installed a user and irssi package
along with some configuration.  We used the `--config` and `--override-runlist` to override
Chef's defaults for cookbook and runlist locations.  Think of a cookbook as a collection of recipes.

### Install Git with Nodes and Cookbook Dependencies

1. Read docs for `knife cookbook site`

    $ cd
    $ mkdir ~/cookbooks
    $ echo "cookbook_path "#{ENV['HOME']}/cookbooks" > ~/.chef/solo.rb
    $ mv ~/irc ~/cookbooks

2. Download `git` recipe from community site

    $ knife cookbook site download git
    $ tar xzvf git-*.tar.gz -C cookbooks/

3. Read `metadata.rb` and download dependent cookbooks

    %w{ dmg build-essential yum windows }.each do |cookbook|
      depends cookbook
    end
    depends "runit", ">= 1.0"

    $ for dep in dmg build-essential yum windows runit; do
    >   knife cookbook site download $dep;
    >   tar xzvf $dep*gz -C cookbooks;
    > done

4. Ensure all cookbooks are on the cookbook path

    $ cd cookbooks
    $ grep depends */metadata.rb
    ...
    depends chef_handler
    ...
    $ knife cookbook site download chef_handler && tar xzvf chef*gz -C cookbooks

5. Search docs for `dna.json` and create it with run list

    # ~/.chef/dna.json
    {
      "run_list": ["recipe[irc]", "recipe[git]"]
    }

6. Run `chef-solo`

    $ sudo chef-solo --config ~/.chef/solo.rb --json-attributes ~/.chef/dna.json

7. Clone `https://github.com/opscode/chef-repo.git`
8. Create `chef-repo` and set remote origin, keep your infrastructure code in Git repo

    $ cd chef-repo
    $ git remote set-url origin git@github.com:atlanta-cookbooks/tdi-example
    $ git push -u origin master

## Using Chef with Tools

This chapter covers on how to aid your infrastructure code using common tools: Ruby and VirtualBox.
Along the way, you'll learn about `chef-client`, nodes, and running chef server.

### Ruby

1. Create Opscode community login and setup account
2. Download `knife.rb` configuration file and setup local environment

### VirtualBox

## An Introduction to TDD and BDD

## A Test-Driven Infrastructure Framework

## Test-Driven Infrastructure: A Recommended Toolchain
