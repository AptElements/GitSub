# GitSub: WebSub for Git

Would it not be nice if you could just do this?

```shell
$ git clone https://github.com/AptElements/websub4git
$ cd websub4git
$ git-watch -- 'git pull && make clean deploy'
```

When a push to the repository is made, it will pull, build and then deploy your code.

# Rationale

Mechanisms such as GitHub WebHooks are proprietary and cannot be interfaced with standard tools. GitSub comes from a strong desire to receive updates from any Git hosting service, in a standardised, plug-and-play way.

# Specification

[WebSub is a W3C recommendation](https://www.w3.org/TR/websub/) that provides a "common mechanism for communication between publishers of any kind of Web content and their subscribers, based on HTTP web hooks". GitSub concerns itself with crossing WebSub and Git repositories.

A GitSub-compatible Git repository requires:
1. An HTML page ("topic") that describes this repository.
   1. It meets the WebSub specification by having links that are `rel="self"`, `rel="hub"`.
2. When an update is made to the repository:
   1. The topic's `updated` meta-tag's contents are changed.
   2. The "hub" [is notified](https://www.w3.org/TR/websub/#publishing).
3. `<link rel="git">` tags are optional.

## Example "topic" HTML page

```html
<html>
  <head>
    <title>...</title>
    <link rel="self" href="http://example.com/sample-repo-reference">
    <link rel="hub" href="http://example.com/websub-hub">
    <link rel="git" href="git@github.com:ScalaWilliam/git-watch.git"/>
    <link rel="git" href="https://github.com/ScalaWilliam/git-watch.git"/>
    <meta name="updated" content="never"/>
  </head>
</html>
```

# Client-side access

The specification does not concern with client-side event streams. Please see:
- http://git.watch/
- [websub-to-eventsource](https://github.com/scalawilliam/websub-to-eventsource)
- [websub-execute](https://github.com/ScalaWilliam/websub-execute).

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
