+++
date = "2019-04-22T20:00:00+03:00"
description = ""
title = "Being new in a software project"
categories = ["Software"]
weight=1

+++

Throughout my career, a few times,

- I joined a new project
- I became one of the main maintainers of the project after a while 
- I helped new people to adapt the project

The companies were different. The teams were different. The technology stacks were different. However, I realized my approach was similar. I think I can say some things about being new in a software project. In this blog post, I am going to share "my way".

<!--more-->

- First of all, don't judge people and decisions from day one. Some decisions/design choices/solutions may seem silly to you. Without understanding the problems well and without knowing the conditions under which the decisions were taken, your opinions are -most probably- so poor. Wait until you get the everyting. Just try to adapt the team/the team's way in this period. After a while, you will notice

  - Some of the weird decisions are not actually weird. There are some logical explanations. -> Your opinion was wrong.
  - Some of the stupid solutions are not actually so stupid. There are some tradeoff between alternative solutions but the difference is not so much. It is not a big failure to select the wrong one. -> You overestimated the failure.
  - Some of the incompetent implementations have some reasonable excuses. -> Your judgement was not fair.
  - Some of the mistakes are real mistakes. -> That is why you are there to contribute the team and the project!

- Don't try to understand everything from day one. There may be thousands lines of code. The stack may be totally new to you. The architecture may be so complicated to understand in a few days. The project may require deep domain knowledge. Nobody expects you to get the everything in a week. Give enough time to yourself. Software is more than the boxes in the white board and it requires huge amount of effort to reveal itself.

- Don't wait for getting everything to get a task. Pick the easiest one and try to do it. For example, adding a new button/endpoint/service/flag which is almost same as one of the existing ones. While doing this task, try to understand which kind of activities you need to finish a task. Implementing production code, implementing unit/integration tests, code review, adding new sections into internal/user documents, doing some configurations in some other tools/systems, conducting necessary negotiation with other stakeholders, security scan, bringing it into production (or internal usage) etc. DO NOT rush. Juniors sometimes try to finish the task as soon as possible to show their performance. This is a very common conversation:


        Newbie: Hey. I am done😎Only in a half day.

        Veteran: Hımmm. Did you do X?

        Newbie: Not yet😳

It is ok to make this conversation a few times. However, you should know what to do in order to finish a task after a while. You should be able to finish tasks in every aspects without bothering the other people so much. If your team has a proper, well-written definition of done, you are so lucky :) 

- Every task is an opportunity to learn new things. If you are working in an insightful team, given time for a task includes enough amount of time to increase your knowledge about the project. Don't try to learn everything with a task but ensure every task is a step in your level. You can select one aspect of the task and learn it before delivering the output. For example, inheritance in Go, services in k8s, threads in Python etc. Read documentation about the topic, break somethings to understand the effects, question the design and ask your teammates if there are some open points. Don't ask so much at a time :) I always keep a map in my mind about which topics can be asked to whom and use "Round-robin" to distribute my questions. 

- Invest in your skills and knowledge about the technology stack and the domain. Learn deeply regardless of whether they are necessary for the current tasks or not. You should be one of the "go-to people". If you are working with Java, be the one of the people who try newest Java version. If you are working with kubernetes, follow the CNCF tools closely. Even in your personal time. Tasks/roles/companies/projects are temporary. Knowledge and skills are lasting. Prepare yourself for the next nevel. Never stuck at your current level.

- Shadow decision makers. 