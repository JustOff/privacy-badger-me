# IMPORTANT 
This version of Privacy Badger is the legacy version (pre-WebExtensions) which has been released to us by Cooper Quintin (cjq at eff dot org) to make a community fork of under the same name for Pale Moon use.

This version currently does not have a maintainer. If you feel you can help, our door is open!


## Introduction

Privacy Badger is a add-on that blocks spying ads and invisible trackers as you browse. [More info here.](https://www.eff.org/privacybadger)

## How heuristic blocking works

This is a rough summary of Privacy Badger's internal logic for blocking trackers. At the moment, "tracker" == "third-party cookie from a site that tracks you on multiple first-party origins." I am in the process of adding support for other non-cookie tracker types (local storage, etags, cache hits, etc.).

Privacy Badger uses a (relatively-simple) heuristic algorithm for deciding whether a third-party is tracking you. When Privacy Badger sees a third-party request on a website, it checks:

1. Does the third-party read a cookie? If not, don't count it in the blocking heuristic. Otherwise:
2. Is the cookie sufficiently high-entropy? If not, don't count it. (Currently the entropy calculation is *very* crude! See lib/heuristicBlocker.js.) Otherwise:
3. Increment the heuristic blocker counter by +1 for that domain. Has the base domain (eTLD+1) of the third-party read cookies on at least 3 first-party base domains? If not, don't block it (for now). Otherwise:
4. Has the third party posted an acceptable DNT policy? (We check this using an XML HTTP Request to a well-known path where we are asking sites to post statements of [compliance with DNT](https://www.eff.org/dnt-policy).) If so, don't block it. Otherwise:
5. Is the third party or any of its parent domains on a preloaded whitelist of sites to not block because it would probably cause the first-party site to break? If so, block it from reading cookies in a third-party context. Otherwise:
6. Block third-party requests from the third-party entirely.

In addition, Privacy Badger will block third-party cookies from a domain if any of its parent domains have been blocked or cookie-blocked.

Note that users can manually set domains to be unblocked (green), cookie-blocked (yellow), or red (blocked). These choices *always* override the heuristic blocker.

By default, Privacy Badger sends the Do Not Track header on all requests. It also clears the referer for all requests that are cookie-blocked.

## Developer guide

### Getting started
1. Install the `jpm` package using npm. `npm install -g jpm`
2.  Once the SDK is activated, run `jpm -b <path/to/palemoon> run` to launch a clean Pale Moon profile with Privacy Badger installed. Run `jpm -b <path/to/palemoon> test` to run tests. `jpm xpi` creates a package (.xpi file) that you can install by loading into Firefox.

### Contributing

Before you submit a pull request please consult the [CONTRIBUTING.md](doc/CONTRIBUTING.md) file.
