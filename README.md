# Git Watch protocol: unified approach to a reactive Git

We compose several components and protocols to achieve a reactive Git, where you can simply do the following:

```
$ git-watch -- 'git pull && make clean deploy'
```

... and when your Git repository updates, it will automatically get pulled and deployed.

What you get is an unlimited ability to interface with your Git repository changes, regardless of which Git host you use.

# Git Watch

The key component is [WebSub, a W3-recommended standardised alternative to webhooks](https://www.w3.org/TR/websub/).

The protocol has the following requirements:
1. An HTML page ("topic") which describes the repository.
2. That when an update is performed:
  1. The `updated` `meta`-tag's `content` is changed.
  2. The `hub` [is notified](https://www.w3.org/TR/websub/#publishing) that the "topic" has been updated.
3. The HTML page optionally has `git` tags, for any clients interested in checking out via SSH for example.

```html
<html>
  <head>
    <title>Sample repository reference</title>
    <link rel="self" href="http://example.com/sample-repo-reference">
    <link rel="hub" href="http://example.com/websub-hub">
    <link rel="git" href="git@github.com:ScalaWilliam/git-watch.git"/>
    <link rel="git" href="https://github.com/ScalaWilliam/git-watch.git"/>
    <meta name="updated" content="never"/>
  </head>
</html>
```

## Client-side access

WebSub can be turned into an event stream, for example with [websub-to-eventsource](https://github.com/scalawilliam/websub-to-eventsource). Client-side commands can be executed based on WebSub updates, for example with [websub-execute](https://github.com/ScalaWilliam/websub-execute).

## Retrofitting with GitHub

A request will be made for GitHub to implement this protocol. While waiting, a Git Watch—GitHub bridge will be implemented.

The bridge will provide a "Git topic" for each repository. GitHub can push to that topic if it wishes to do so.

# Alternatives

## Can't I just poll?

Yes, you can, but with drawbacks:
1. Reaction time could be as long as the polling interval.
2. Redundant requests performed, each resulting in no update.

## Can't I just use GitHub (or GitLab/BitBucket/...) webhooks?

Yes, you can, but with drawbacks:
1. Setting up a server (nginx, firewalls) to listen to GitHub webhooks.
2. Verifying that the request client is indeed from GitHub.
3. Maintaining/versioning a script that does these verifications.

## Can't I just use Travis to auto-deploy?

Yes, you can, but with drawbacks:
1. You'd have to hand your credentials to Travis.
2. Travis uptime is out of your own hands.
3. Not easily reproducible locally.

## Can't I just use Jenkins to auto-deploy?

Yes, you can, but with drawbacks:
1. Jenkins has its own lifecycle.
2. Jenkins has to be set up and maintained like any service.

