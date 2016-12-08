---
layout: post
title: "Application Design checklist"
author: "Kshitij Banerjee"
avatar: "img/authors/K_icon.png"
description: "List of topics that you should check off in your design. A list I hope to keep updated"
img: "Business-Checklist-ICV.jpg"
color: 3f1653
---

  copiable direct link => [/2016/03/12/design-checklist.html](Blog/2016/03/12/design-checklist.html)

List of topics that you should check off in your design
=======================================================


Every often you design an application, its easy to miss on some basic parts of the application because you're engrossed trying to fix the main problem at hand.
Here's a small checklist of items (That I hope to keep updating), for things to look out for once you complete your next application design.

## Checklist
1. Idempotency
 - Is there a business idempotence key?
 - Is there a consistent message id for communication between services?
2. Versioning
 - How are multiple versions of the code being handled ?
 - URI vs Header based api versioning?
3. Cutoff plan
 - When you launch, some data may need the old version of the code to bring to completion.
 - Are you doing a big bang? Can you version at a more granular level and make the transition seamless?
4. Data partitioning
 - Is your data model considering the sharding needs?
 - Is there an specific shard-key that you might need to use?
5. Data Isolation
 - In multi-tenant applications, will you be able to ensure data isolation between tenants?
6. Security
 - Passwords, SSL, input is always malicious etc.
7. Validations
 - Are you validating your assumptions ?
8. Concurrency issues
 - Keeping a counter in your online transactions ? Is that a write bottleneck?
9. ID generation and uniqueness
 - Are your ID's unique between systems ?
 - Are your ID's self describing ?
10. Auditing and Logging
 - Are changes to records tracked in history ?
 - Are you logging the information you'll need to debug issues?
11. Message queues and Asynchronous communication
 - Self describing
12. Remote calls.
 - Using facades?
13. Data Archival.
 - Are you able to identify the active to the passive data ?
 - How will your archive your data from tier-1 to tier-2
14. Abstraction for loose coupling
 - Dependency Injection or Inversion of Control?
15. Fault tolerance
 - How do you recover from a temporary node failure ?
 - Does your data store have WAL logs ?
16. Failure Recovery
 - How does your architecture deal with permanent node failures?
 - Can you replay events from a previous checkpointed snapshot?
 - Do you have a sync replica to fall over to ?
