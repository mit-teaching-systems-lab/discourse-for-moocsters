# discourse-for-moocsters
Instructions for setting up Discourse for a TSL/STEP MOOC course on EdX.

**This is still in progress, if you want to do this in the next week or two, email Kevin or come talk in person.**

## Overview
Our MOOCs run on EdX or EdX Edge, and use Discourse for discussion forums instead of the built-in EdX forums.  This works by creating a new Discourse instance hosted in Digital Ocean, configuring it with plugins, and then setting up the EdX course to launch the forums through LTI.  This lets learners move into the discussion forums seamlessly, and let's course instructors make modifications in how our discussion forums work that can be replicated across all our courses.  One example is letting learners create their own discussion "groups" based on their interests.

This repository describes how to set up a Discourse for a new TSL/STEP MOOC course on EdX.  There are two other related repositories:

- [discourse-edx-lti](https://github.com/mit-teaching-systems-lab/discourse-edx-lti) is a Discourse plugin for LTI integration with EdX
- [discourse-tsl-mods](https://github.com/mit-teaching-systems-lab/discourse-tsl-mods) is a Discourse plugin with features, styling and settings that are the defaults for new TSL/STEP MOOCs

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
- Follow [Install Discourse in under 30 minutes](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md).  We've typically run standard droplets with 2GB RAM / 1v CPU / 50GB disk.  Add backups and monitoring as options, and use separate SSH keys for each instance.
- If you didn't setup SSL during the initial installation, follow the Discourse instructions to setup SSL with Let's Encrypt ([instructions](https://meta.discourse.org/t/setting-up-lets-encrypt/40709))
- Wait to setup additional [features](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md#add-more-discourse-features) until after the plugin and EdX setup is done.
- When the site is up, set it as "private" login and don't invite any users yet
- Test!
- Install the two plugins and follow the setup in those repositories, in order:

  1. [discourse-edx-lti](https://github.com/mit-teaching-systems-lab/discourse-edx-lti)
  2. [discourse-tsl-mods](https://github.com/mit-teaching-systems-lab/discourse-tsl-mods)
- Test!
- Add your course to the MIT-internal [README: Course Forums](https://docs.google.com/document/d/1rcsh2yl8QgTvmZ8-RvIt4ljl21t-oZkfcf7m97uqy1o/edit#heading=h.ugwekbftejfr).


## Ongoing maintenance
- Submit billing to finance
- Rollbar
- Discourse upgrades

## Analysis of course data
[in other docs, still needs to be added here]

## Archiving a course
[in other docs, still needs to be added here]
