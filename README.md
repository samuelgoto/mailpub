# MailPub

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

![](static/mailpub3.svg)

# Design

![](static/mailpub1.svg)
![](static/mailpub2.svg)
![](static/mailpub4.svg)
