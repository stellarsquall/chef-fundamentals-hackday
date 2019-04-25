# Chef Fundamentals Training Hack-Day Resources
Resources for Chef Fundamentals training hack-day

## Purpose

This repo is designed to accompany the hands-on Chef Fundamentals training course. The hack day provides an opportunity for learners to work in individually or in groups on a project of their choosing with guidance from their instructor.

## Lab Environment

The learner's lab environment will vary depending on their choice of exercises. The instructor may have shut down learner's instances from the previous days of training, so check with your instructor for instance IPs depending on what exercise you choose to work on.

Your instructor will provide you with login information for the Windows and Centos instances to be configured. The cookbook code itself is _not_ intended to be executed on a learner's local machine. Do not run the chef-client on your local workstation.

## Resources

The training references the [Chef Documentation](https://docs.chef.io) to encourage learners to look up information and become self-sufficient in troubleshooting. If the documentation cannot be accessed behind your firewall, you can alternatively access the [chef-web-docs](https://github.com/chef/chef-web-docs) GitHub page, or the older [Chef12 DevDocs](https://devdocs.io/chef~12/) pages for reference. If none of these are accessible, please make this known to your instructor.

Time to become Sous Chefs. 

## Beginner Exercises

_If you are new to Chef and found this week's training to be intensive, consider the beginner exercises to start with on the hack day._

1. Setting up Test Kitchen on your local machine.
   * This week's exercises can be recreated on your local machine. If you need to, grab the cookbook's you've been working with from this repo:
      * [Chef Fundamentals Cookbooks](https://github.com/stellarsquall/chef-fundamentals-cookbooks)
   * Set up Test Kitchen using one of the following drivers:
     * [kitchen-vagrant](https://github.com/test-kitchen/kitchen-vagrant) _This is the recommend option for beginners, especially if using a Windows workstation_
     * [kitchen-dokken](https://github.com/someara/kitchen-dokken) or [kitchen-docker](https://github.com/test-kitchen/kitchen-docker)
     * [kitchen-ec2](https://github.com/test-kitchen/kitchen-ec2)
       * Note that this will be the most difficult driver to deploy, as it involves integrating AWS EC2 with your local workstation. You will need an AWS account, the AWS Command-Line Interface (CLI), and depending on your strategy a special EC2 Role to allow permissions to create instances might be needed. Ask your instructor for guidance if you need help getting started.
    * The linked Github pages provide documentation for the settings for each of these drivers.
    * If using docker, consider using kitchen-dokken, which ships with Test-Kitchen. kitchen-docker and other drivers not included with the ChefDK can be installed with:
      * `chef gem install kitchen-docker`

2. Deploy your apache cookbook using your driver of choice.
   * You will need to configure the cookbook's .kitchen.yml file. Note that the week's exercises were mostly intended for centos machines.
   * Using the `kitchen` commands, create, converge, verify, and test each cookbook.
   * You will see the default tests for each cookbook deployed when running `kitchen verify`, and may experience failures.
   * Once you successfully deploy a centos testing instance with `kitchen converge`, write some inspec tests for your recipes.
      * Check the documentation for these [inspec resources](https://www.inspec.io/docs/reference/resources/) to get started:
         * port
         * file
         * command
         * service (this may not work on centos due to systemd, try the command resource if you have issues)
      * Write some simple tests for the desired state, and deploy them with `kitchen verify`. Don't forget that you can reuse the same instance while testing. `kitchen converge` will run the chef-client, while `kitchen verify` deploys your tests. On occasion, it may be useful to run `kitchen destroy` and start over with a clean state.
   * Don't forget about the `kitchen login` command to help you with troubleshooting.

3. Set up a multi-platform apache cookbook
   * Once you've got apache working, try to make your cookbook work for Ubuntu 16.04
     * Configure the driver in .kitchen.yml
     * Don't forget that you can target individual instances with the kitchen commands, like:
       * `kitchen create ubuntu`
       * `kitchen verify centos`
   * A [case statement](https://docs.chef.io/ruby.html#case) in your attribute file is usually used for this. You can then pass these platform-specific attributes to your resources within the server recipe.

## Intermediate Exercises
_The intermediate exercises are intended for learner's that felt comfortable with the coding exercises over the week's training._

1. Experiment with creating a load balancer
   haproxy is a common package for setting up a load balancer. There are two approaches you could take:
   1. Writing a recipe from scratch
        * An incomplete example of a recipe that dynamically searches for webservers was shown in the slides. You could try this by locating a generic haproxy.cfg template on the web, and passing webserver IPs into it.
   2. Using the [haproxy community cookbook](https://supermarket.chef.io/cookbooks/haproxy)
        * This is the more real-world approach, in which you utilize custom resource like `haproxy_install` to set up a custom installation for haproxy.
        * This approach requires extensive testing, so I recommend being familiar with test-kitchen if you'd like to try it.
        * Hints: 
          * often, community cookbooks include common setup scenarios in their test/ directory on the Github page for the cookbook. 
          * Locating the [haproxy](https://github.com/sous-chefs/haproxy) test/fixtures/cookbooks/test directory will provide you with some sample recipes that the authors use when testing their own cookbooks, and may provide you with guidance in using some of the custom resources. 
          * The resources/ directory of a cookbook usually provides usage for a resource, but you should be comfortable reading the source code to discover these options if you find the readme to be lacking enough detail.

2. Deploy three instances to test it out!
   * You will likely need your instructor to provide you with EC2 instances, unless you'd like to create your own.
   * You're welcome to try adding both windows and linux instances to your pool, but ask your instructor if you'd like these spun up instead of using centos.
   * During the development phase, you can test your load-balancer code using test kitchen instead of directly on an instance, but either approach is acceptable. 


## Advanced Exercises
_Advanced exercises are intended for learner's who have worked with Chef before, and are comfortable with debugging and troubleshooting code. Experience with Test Kitchen is recommended._

1. Write and test a database cookbook.
   * Select a database technology and use a community cookbook to set up a simple implementation.
   * Some options include:
     * [mysql](https://supermarket.chef.io/cookbooks/mysql)
     * [sc-mongodb](https://supermarket.chef.io/cookbooks/sc-mongodb)
     * [postgresql](https://supermarket.chef.io/cookbooks/postgresql) (recommended)
   * Note: the database cookbook, although useful, has been deprecated and is not recommended for production systems anymore. This functionality is being ported to custom resources within individual database cookbooks.

2. This scenario should be tested with kitchen, instead of using dedicated AWS instances.
   * The components needed are usually involves setting up:
     * A database instance
     * Database users
     * A testing suite
   * You may find an example repo here that can guide you in getting started:
     * https://github.com/stellarsquall/chef-repo_postgres
     * Notes: this repo has _not_ been tested in quite some time, so don't expect it to work out of the box if cloned. I used vagrant and virtualbox for my testing, and (insecure) databags for database passwords.
   
3. An advanced version of this exercise would involve creating an entire LAMP stack (Linux, Apache, MySQL, PHP) application scenario
   * This option will likely be too complex to complete during a single hackday. If you've written these components before and feel comfortable with setting up a database, go for it!
   * Set up a simple deployment of a PHP application that makes calls to a MySQL database. This would be entirely self-guided.
   * You may find an example repo I've created a while ago to be helpful in getting started:
     * https://github.com/stellarsquall/chef-repo_lamp
     * Notes: this repo has _not_ been tested in quite some time, so don't expect it to work out of the box if cloned. I used vagrant and virtualbox for my testing, and (insecure) databags for database passwords.