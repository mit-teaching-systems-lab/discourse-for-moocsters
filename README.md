# discourse-for-moocsters
Instructions for setting up Discourse for a TSL/STEP MOOC course on EdX.

**This is still in progress, if you want to do this in the next week or two, email Kevin or come talk in person.**

## Overview
Our MOOCs run on EdX or EdX Edge, and use Discourse for discussion forums instead of the built-in EdX forums.  This works by creating a new Discourse instance hosted in Digital Ocean, configuring it with plugins, and then setting up the EdX course to launch the forums through LTI.  This lets learners move into the discussion forums seamlessly, and let's course instructors make modifications in how our discussion forums work that can be replicated across all our courses.  One example is letting learners create their own discussion "groups" based on their interests.

This repository describes how to set up a Discourse for a new TSL/STEP MOOC course on EdX.  There are two other related repositories:

- [discourse-edx-lti](https://github.com/mit-teaching-systems-lab/discourse-edx-lti), a Discourse plugin for LTI integration with EdX
- [discourse-tsl-mods](https://github.com/mit-teaching-systems-lab/discourse-tsl-mods), a Discourse plugin with defaults for new TSL/STEP MOOCs

## How do I do this?
This requires some knowledge of ops/sysadmin engineering tasks.  There's actively maintained documentation for much of this setup process, so this should be fairly accessible.  Note that this is a different kind of engineering work than programming or coding.

To do this end-to-end, you'll need access to these accounts and services:

- Cloud provider (eg, Digital Ocean)
- Email provider (eg, Mailgun)
- DNS provider (eg, AWS Route 53)
- Key-value storage provider (eg, AWS S3)

## Setup
#### Initial setup for new course forums
- Setup [MailGun](https://www.mailgun.com/) or another email provider.
- Add DNS records for email and for your new Discourse instance (see [Discourse docs](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md#email)).  We've typically used AWS Route 53 for this and domain names like `graduate-of-the-future-discourse.teachingsystemslab.org`.
- Follow [Install Discourse in under 30 minutes](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md).

  1. We've typically run standard droplets with 2GB RAM / 1v CPU / 50GB disk.  Add backups and monitoring as options, and use separate SSH keys for each instance.
  2. If you didn't setup SSL during the initial installation, follow the Discourse instructions to setup SSL with Let's Encrypt ([instructions](https://meta.discourse.org/t/setting-up-lets-encrypt/40709))
  3. When the site is up and you go through the setup process, set it as "private" login.  Set the "Homepage" to be `Categories` and then be prepared to make the most important decision: what emoji style to use.  Skip inviting any users yet - we'll do that through EdX and LTI.
  4. Wait to setup additional [features](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md#add-more-discourse-features) until after the plugin and EdX setup is done.
  5. Test!  At this point, you should be able to sign in with your admin user and see a Discourse forum.

- Install Discourse plugins.  Follow the steps in those repositories, or check out the [Install a Plugin](https://meta.discourse.org/t/install-a-plugin/19157/1) Discourse docs for more help.  You can install both in one shot, and then configure them in order.

  1. [discourse-edx-lti](https://github.com/mit-teaching-systems-lab/discourse-edx-lti)
  2. [discourse-tsl-mods](https://github.com/mit-teaching-systems-lab/discourse-tsl-mods)

- Test!
- Install additional [features](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md#add-more-discourse-features) or read up on other [admin configuration](https://github.com/discourse/discourse/blob/master/docs/ADMIN-QUICK-START-GUIDE.md).

  1. Automatic backups to S3: https://meta.discourse.org/t/configure-automatic-backups-for-discourse/14855
  2. Create a new Rollbar project to report any JS errors.  Add the Rollbar JS snippet in `Admin` > `Customize` > `Themes` and make a new theme with that JS in the `</head>` section.

- Test!
- Add your course to the MIT-internal [README: Course Forums](https://docs.google.com/document/d/1rcsh2yl8QgTvmZ8-RvIt4ljl21t-oZkfcf7m97uqy1o/edit#heading=h.ugwekbftejfr).
- [Configure](https://github.com/discourse/discourse/blob/master/docs/ADMIN-QUICK-START-GUIDE.md) whatever else you like or add some more [plugins](https://meta.discourse.org/c/plugin) or [features](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md#add-more-discourse-features)!

  1. Customize text
  2. Change colors


## Ongoing maintenance
- Submit billing to finance
- Rollbar
- Discourse upgrades

## Analysis of course data
If you'd like to get course data for analysis, you can do this!  You'll need have someone give you access credentials and then run some commands using these tools, described more fully below:

1. SSH and SSH keys
2. Docker
3. Rails console

### Gaining access to production data
#### 1. SSH and SSH keys
Course instances of Discourse are typically hosted on Digital Ocean and you can use `ssh` to connect to the instance.

You'll need help from someone who has access to the lab's Digital Ocean account, but it will only take a few minutes and you don't need to be in the same room.  This article will walk you through it: [How To Use SSH Keys with DigitalOcean Droplets](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets).

⚠️ If you are sharing SSH keys, never share the private key anywhere.  If you do, you are giving full access to all course data and the ability to destroy everything. ⚠️

If you connect with ssh succesfully, you should see a command prompt like this:

```
graduateofthefuture-discourse:~#
```

#### 2. Docker
We use the simplest Discourse setup, where Discourse runs inside of a Docker container.  So once you're connected over `ssh`, you need to enter into the Docker container.

Run these commands to enter the container:
```
graduateofthefuture-discourse:~# cd /var/discourse/
graduateofthefuture-discourse:/var/discourse# ./launcher enter app
graduateofthefuture-discourse-app:/var/www/discourse#
```

Now you're inside the container!

#### 3. Rails console
Once there, you can use tools for interacting with the Discourse app.  Discourse is a Ruby on Rails app, so the most useful tool is `rails console`, which lets you run code to interact with the Discourse app or the database it's connected to.

From here, you can run any Rails code that you like to query the database for the forums however you like.

⚠️ You can also delete all the data or do destructive things in the process, so be mindful of what kinds of queries you run, especially if they write, create or update any data. ⚠️

Once you're in the Docker container, run these commands to start a Rails console:

```
graduateofthefuture-discourse-app:/var/www/discourse# rails console
[1] pry(main)>
``` 

Now you're inside a Rails console and run commands to interact with the production Discourse instance, or to query from other related services like the database.

### Queries to get particular kinds of data
#### Grab a CSV of all data for a category
This prints a CSV of all data for a particular category.  To use it, you pass the "slug" of the category.  You can find the "slug" for a category by looking in the URL for the page you want data for.

For example, with the URL:
```
https://graduate-of-the-future-discourse.teachingsystemslab.org/c/part-2-focusing-on-the-graduate-profile-process/activity-i-used-to-think-but-now-i-think
```

The slug is the last part, `'activity-i-used-to-think-but-now-i-think'`.  So to run the script on that category below, you'd paste this whole script below into the console, and then run:

```
[1] pry(main)> print_csv('activity-i-used-to-think-but-now-i-think')
```

Then you should see a CVS!  The simplest way to get it to your computer is to copy it and paste it somewhere else.

ℹ️ When you're all done, run `exit` to leave the Rails console, `exit` to leave the Docker container, and then `exit` to leave the SSH session.

```ruby
require 'csv'

# Returns CSV string
def category_to_csv(category)
  keys = topic_keys  
  CSV.generate do |csv|
    csv << keys
    category.topics.each {|topic| csv << topic_to_array(topic, keys) }
  end
end

def topic_hash(topic)
  {
    url: topic.url,
    posts_count: topic.posts.size,
    posts_combined_text: topic.posts.map(&:cooked).join("\n\n"),
    posts_combined_attachment_count: topic.posts.flat_map(&:attachment_count).sum, # this is a slow operation (adding it adds ~10 seconds to run this for a whole course)
    users_count: (topic.posts.map(&:user_id) + [topic.user_id]).uniq.size
  }.merge(topic.as_json)
end

def topic_keys
  topic_hash(Topic.first).keys
end

def topic_to_array(topic, keys)
  hash = topic_hash(topic)
  keys.map {|key| hash[key]}
end

# Output a CSV with these headers:
# url,posts_count,posts_combined_text,users_count,id,title,last_posted_at,created_at,updated_at,views,posts_count,user_id,last_post_user_id,reply_count,featured_user1_id,featured_user2_id,featured_user3_id,avg_time,deleted_at,highest_post_number,image_url,like_count,incoming_link_count,category_id,visible,moderator_posts_count,closed,archived,bumped_at,has_summary,vote_count,archetype,featured_user4_id,notify_moderators_count,spam_count,pinned_at,score,percent_rank,subtype,slug,deleted_by_id,participant_count,word_count,excerpt,pinned_globally,pinned_until,fancy_title,highest_staff_post_number,featured_link
def print_csv(category_slug)
  category = Category.where(slug: category_slug).try(:first)
  puts category_to_csv(category)
  nil
end
```

## Archiving a course
[in other docs, still needs to be added here]


## Experimetnal: Cloning a site
See [this](https://meta.discourse.org/t/move-your-discourse-instance-to-a-different-server/15721/20) or [this](https://meta.discourse.org/t/is-there-any-way-to-restore-your-site-from-backup-in-the-terminal/42377/2).

Example:
```
# backup file needs to be in public/backups/default
# (this particular folder isn't actually public)
script/discourse restore <filename.of.the.backup.tar.gz>
```

Note that after restoring,, there are several production settings you might need to change, like backups to S3 buckets or HTTPS only.

Possibly relevant settings:
- Files > S3 keys
- Backups > Disable S3 backups

HTTPS warning:
```
WARNING: force_https is enabled in dev
It is very unlikely you are running HTTPS in dev.
Without HTTPS your session cookie will not work
Try: bin/rails c
SiteSetting.force_https = false
```

## Local development
You can develop with Vagrant ([see Discourse docs](https://github.com/discourse/discourse/blob/master/docs/VAGRANT.md)).  As you develop, clear the ERB cache, copy this repository to the `plugins` folder and restart Rails to see changes.

On OSX you may also need to enable NFS for volume sharing, by adding an option to the Discourse Vagrantfile:

```
config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", nfs: true
```

Example (also using `discourse-edx-lti`):
```
rm -rf tmp/cache && \
rm -rf plugins && \
git checkout head -- plugins && \
rsync -av --exclude .git \
  ~/github/mit-teaching-systems-lab/discourse-edx-lti \
  ./plugins/ && \
rsync -av --exclude .git \
  ~/github/mit-teaching-systems-lab/discourse-tsl-mods \
  ./plugins/ && \
vagrant ssh -c 'cd /vagrant && bundle exec rails s -b 0.0.0.0'
```
