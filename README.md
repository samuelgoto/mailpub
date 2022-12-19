# mailpub

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


```
   bob+newsletter@list.com           bob@email.com                     alice@foo.com

   ┌───────────┐                     ┌────────────┐                   ┌─────────────┐
   │           │                     │            │                   │             │
   └─────┬─────┘                     └─────┬──────┘                   └────────┬────┘
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
