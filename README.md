# discourse-for-moocsters
Instructions for setting up Discourse for a TSL/STEP MOOC course on EdX

## Overview
Our MOOCs run on EdX or EdX Edge, and use Discourse for discussion forums instead of the built-in EdX forums.  This works by creating a new Discourse instance hosted in Digital Ocean, configuring it with plugins, and then setting up the EdX course to launch the forums through LTI.  This lets learners move into the discussion forums seamlessly, and let's course instructors made modifications in how our discussion forums work that can be replicated across all our courses.  One example is letting learners create their own discussion "groups" based on their interests.

This repository describes how to set up a Discourse for a new TSL/STEP MOOC course on EdX.  There are two other related repositories:

- [discourse-edx-lti](https://github.com/mit-teaching-systems-lab/discourse-edx-lti) is a Discourse plugin for LTI integration with EdX
- [discourse-tsl-mods](https://github.com/mit-teaching-systems-lab/discourse-tsl-mods) is a Discourse plugin with features, styling and settings that are the defaults for new TSL/STEP MOOCs

## Setup
#### Initial setup for new course forums
- Follow [Install Discourse in under 30 minutes](https://blog.discourse.org/2014/04/install-discourse-in-under-30-minutes/) (or deploy on your own setup)
- Follow the Discourse instructions to setup SSL with Let's Encrypt ([instructions](https://meta.discourse.org/t/setting-up-lets-encrypt/40709))
- Setup [MailGun](https://www.mailgun.com/) or another email provider
- Add DNS records for email and for your new Discourse instance
- Wait to setup additional [features](https://github.com/discourse/discourse/blob/master/docs/INSTALL-cloud.md#add-more-discourse-features) until after the plugin and EdX setup is done.
- When the site is up, set it as "private" login and don't invite any users yet
- Test!

