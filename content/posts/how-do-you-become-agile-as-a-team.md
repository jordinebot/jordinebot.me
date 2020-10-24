---
title: "How do you become Agile as a team?"
date: 2018-05-08T08:26:39+02:00
tags: [agile, product management]
draft: false
---
In the last years, both as a freelancer or working for a company, I've only been part of teams who produced using the
classic waterfall methodology. I feel like in many cases, for the kind of projects we did, it worked good enough. But
anyway I've always wanted to be part of a small team using some Agile approach.

In front of me there's exactly this kind of challenge. I've started working for a company that owns a viable product
with a legacy spaghetti codebase. The company hired a whole new team with a UX/UI designer, a back end developer, two
front end developers, and a QA engineer. We also have with us another full-stack developer (who has been in the company
for +10 years), the project manager and a Scrum master.

The challenge is huge because we are people from 6 nationalities, located in 5 different countries, in 2 different time
zones. We all have at least 5 years of experience in our current roles but we never worked together before. Also, as a
team, we're switching the tools the company has been using for a while. We are moving from [Pivotal Tracker](https://www.pivotaltracker.com) +
[Gitlab](https://about.gitlab.com/) + [Gitlab CI](https://about.gitlab.com/features/gitlab-ci-cd/) to
[JIRA](https://www.atlassian.com/software/jira) + [GitHub](https://github.com) + [Jenkins](https://jenkins.io/).

As if the above were not enough (and please do not misunderstand me, because I find it very stimulating), there's a
considerable amount of effort that needs to be done to maintain the current code, so we cannot dedicate our whole day
thinking about the new architecture. And, I swear, every update in the current codebase is a pain in the ass.

For the current product we are doing weekly sprints. We plan the sprints on Thursday. We code until next Thursday when
we freeze our current release, and from Friday to Tuesday, we test our issue branches, integrate and release. Of course,
every Thursday a new sprint starts, so all this testing, integrating and releasing stuff is done in parallel with coding
the next sprint issues. And since our CI/CD processes are no that *continuous*, it could be smoother...

The product itself is not that complex but one of the toughest difficulties I've detected is that sometimes we spend so
much time talking about the big picture or the flaws we detect in our current process (and there are lots of them). This
is a rabbit hole that doesn't allow us to be Agile. More over, this is the opposite of being Agile. Or productive.

Breaking this destructive spiral is only a matter of quit overthinking everything and start by facing the more
priority issues to build something that is well done, well documented and it just works. We already started filling
our Backlog with the user stories required to escape the rabbit hole. This is step one. We'll see what happens in the
next few weeks.


