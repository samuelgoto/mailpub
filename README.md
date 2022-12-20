# MailPub

> These is a write down of some mental notes I took from a series of conversations with Joseph Smarr and Gordon Brander

MailPub is an early proposal for an email-based social networking protocol. It leverages the existing, open, federated, mature and resilient deployment of email (SMTP/POP/IMAP) and mailing lists ([LISTSERV](https://en.wikipedia.org/wiki/LISTSERV)) to provide the functionality that you'd typically find in social networks (e.g. following, profiles, etc).

It uses email as a foundation but extends it with parts of [WebFinger](https://webfinger.net/) and [vCard](https://en.wikipedia.org/wiki/VCard) (profiles), [ActivityPub](https://www.w3.org/TR/activitypub/) (social expression, e.g. likes), [IndieAuth](https://indieweb.org/IndieAuth) (`@names`) and [RSS](https://en.wikipedia.org/wiki/RSS).

MailPub tries really hard to work within the constrains of existing email clients / servers and list servers. It exposes a series of enhancements, but for the most part, need to be able to operate within backwards compatibility to email: **any** email user **can** be a MailPub user.

# Background

It seemed to some of us that email and newsletters have a lot what you'd want from an open and decentralized social network:

- For starters, users already know what email is (e.g. bootstraping)
- Naming and addressing is built-into email addresses
- Users get to own the social graph (their contact list)
- Users get to own the data (emails written and received)
- Anyone can join (e.g. anyone can use existing servers, custom domain with existing servers or run their own server)
- It is entirely based on open standards (e.g. POP/SMTP/IMAP and Mime)
- There is a massive amount of infrastructure that deals with spam and abuse protection
- It supports a lot of social topologies (e.g. 1:1, 1:many and many:many)
    - Public following is natural (e.g. subscribing to newsletters)
    - Private 1:1 is built-in (e.g. traditional email)
    - Communities of mailing lists   
- It supports a lot of the social expressions
    - replying to threads
    - reshare/retweet by forwarding
    - notably lacking: likes
- It does public and private relatively well

The parts that seemed to be missing were:

- There is no concept of a user profile (that's where we thought that [WebFinger](https://webfinger.net/), [vCard](https://en.wikipedia.org/wiki/VCard) or [LISTSERV](https://en.wikipedia.org/wiki/LISTSERV) could come in)
- There is no concept of programatically finding how to follow an email address (that's where we thought that [LISTSERV](https://en.wikipedia.org/wiki/LISTSERV) would come in)
- There is no concept of social post types and expression types (that's where we thought that [ActivityPub](https://www.w3.org/TR/activitypub/) would come in)
- There is no concept of a user-owned global identity: user identities are namespaced by email servers (i.e. user@server.com) (that's where we thought that [IndieAuth](https://indieweb.org/IndieAuth) (`@domain.com` => `user@email.com`) would come in)

# Design

At a high level, MailPub is, much like [LISTSERV](https://en.wikipedia.org/wiki/LISTSERV), entirely based on email, so, while this can (and should) be automated, it can also be performed manually. It is ultimatelly a set of conventions of how to write/read email that can be readable by a machine.

```
            bob@email.com                                 alice@foo.com
            ┌───────────┐          (1) Can I follow you?  ┌───────────┐
            │           │◄────────────────────────────────┤           │
            │ email.com │                                 │  foo.com  │
            │           ├────────────────────────────────►│           │
            └─────┬─────┘                                 └─────┬─────┘
                  │       (2) Sure. Subscribe to                │
(5) Hello World!  │       bob+newsletter@list.com               │  (3) Can I subscribe to you?
                  │                                             │  Who subscribes? How many?
                  │                                             │  Archives?
                  │                ┌──────────┐                 │
                  │                │          │                 │
                  └───────────────►│ list.com │◄────────────────┘
                                   │          │ (4) Sure.
                                   └──────────┘
                              bob+newsletter@list.com
```

At its most basic operation, it allows users to:

- [Subscribe](#subscribing) to each other
- [Publish](#publishing) to their subscribers
- [Discover](#discovery) who subscribes to whom

## Subscribing

It leverages the existing battle-tested infrastructure of mailing lists to allow other email users to follow an email user:

```
   bob+newsletter@list.com           bob@email.com                       alice@foo.com
   ┌───────────┐                     ┌───────────┐                       ┌───────────┐
   │           │                     │           │                       │           │
   └─────┬─────┘                     └─────┬─────┘                       └─────┬─────┘
         │                                 │                                   │
         │                                 │                              LISTS│
         │                                 │◄──────────────────────────────────┤
         │                                 │                                   │
         │                                 │                                   │
         │                                 │newsletter bob+newsletter@list.com │
         │                                 ├──────────────────────────────────►│
         │                                 │                                   │
         │                                 │                                   │
         │                                 │                         SUBSCRIBE │
         │◄────────────────────────────────┼───────────────────────────────────┤
         │                                 │                                   │
         │DONE                             │                                   │
         ├─────────────────────────────────┼──────────────────────────────────►│
         │                                 │                                   │
         │Hello World!                     │                                   │
         │◄────────────────────────────────┤                                   │
         │                                 │                                   │
         │                                 │                                   │
         │From: bob@email.com, Hello World!│                                   │
         └─────────────────────────────────┴──────────────────────────────────►┘
```

This is implemented with a series of machine-readable conventions over email, on top of [LISTSERV](https://en.wikipedia.org/wiki/LISTSERV).

For example, say `alice@foo.com` wants to follow `bob@email.com`. Alice, using her existing email client sends an email to `bob@email.com`:

```
From: alice@foo.com
To: bob@email.com
Subject: LISTS GLOBAL
```

Bob, who has an email client that is MailPub aware, receives that email which gets responded automatically to `alice@foo.com` pointing Alice with a machine-readable way to subscribe his newsletter (Bob, before hand, has already set up a `bob+newsletter@list.com` mailing list of his preference):

```
From: bob@email.com
To: alice@foo.com
Subject: Re: LISTS GLOBAL
newsletter bob+newsletter@list.com
```

Here, **newsletter** is a well-known list type, which announces that bob has a **newsletter** at a specific address.

Alice, knowing that this is a MailPub convention, then subscribes to Bob's newsletter knowing that it will accept [LISTSERV](https://en.wikipedia.org/wiki/LISTSERV) commands:

```
From: alice@foo.com
To: bob+newsletter+subscribe@list.com
Subject: Subscribe
```

Which the listserv, having already been configured by Bob to automatically accept members, respond:

```
From: bob+newsletter+subscribe@list.com
To: alice@foo.com
Subject: Welcome!
```

## Publishing

When Bob publishes, he publishes to his `bob+newsletter+subscribe@list.com` by sending an email to it, which is then distributed to all of his followers via the typical mechanisms that mailing lists already provide.

Bob uses a multi-format mime email, so that email users can read his post, but also, if some of his followers can understand [ActivityPub](https://www.w3.org/TR/activitypub/) format it can be augmented:

```
From: bob@email.com
From: bob+newsletter+subscribe@list.com
Subject: Hello World
MIME-Version: 1.0
Content-Type: multipart/mixed;
        boundary="XXXXboundary text"

Welcome to my first blog post my fellow newsletter subscribers!!

--XXXXboundary text
Content-Type: text/plain

this is the body text

--XXXXboundary text
Content-Type: application/ld+json; profile="https://www.w3.org/ns/activitystreams";
{
  "@context": ["https://www.w3.org/ns/activitystreams",
               {"@language": "en-GB"}],
  "type": "Article",
  "name": "Hello World!",
  "content": "Welcome to my first blog post my fellow Mastodon user!",
  "attributedTo": "https://email.com/~bob",
  "to": "https://list.com/~bob+newsletter",
}
--XXXXboundary text--
```

## Discovery

As with any social network, discovering information of a specific user is a fairly key functionality.

The MailPub protocol starts by trying to discover the information with [WebFinger](https://webfinger.net/), but if that is unavailable (as it is expected to be the case with major email servers), it fallback to a [LISTSERV](https://en.wikipedia.org/wiki/LISTSERV) [Info command](https://www.lsoft.com/manuals/16.0/htmlhelp/list%20subscribers/LSCommands.html).

![](static/mailpub4.svg)

## Naming

Email addresses have a lot of great open and federated properties, but in practice, it is really hard to run your own email server. Personal websites, on the other hand, are substantially easier to host and run.

Although hosting your own site is still done by a tiny tiny fraction of people, it seemed worth connecting the dots between [IndieAuth](https://indieweb.org/IndieAuth) and MailPub because it gives you the ability to address users via `@user.com` as opposed to `user@email.com`.

So, when MailPub runs into an user identifier that looks like `@user.com` it fetches the content of `user.com` and looks for a `<link rel="me" href="email@server.com">`. 

```html
  <!-- @user.com is shorthand for email@server.com -->
  <link rel="me" href="email@server.com">
```

        

