+++
date = "2019-04-26T22:00:00+03:00"
description = ""
title = "Adapting to a software project"
categories = ["Software"]
weight=1

+++

Throughout my career, a few times,

- I joined a project
- I became one of the main maintainers of the project/a component after a while 
- I helped new people to adapt to the project

The companies were different. The teams were different. The technology stacks were different. However, I realized my approach was similar. I think I can say some things about adapting to a software project. In this blog post, I am going to share "my way".

<!--more-->

- First of all, don't judge people and decisions from day one. Some decisions/design choices/solutions may seem silly to you at first glance. Without understanding the problems well and without knowing the conditions under which the decisions were taken, your opinions are -most probably- so poor. Wait until you get the everyting. Just try to adapt to the team/the team's way in this period. After a while, you will notice

  - Some of the weird decisions are not actually weird. There are some logical explanations. -> Your opinion was wrong.
  - Some of the stupid solutions are not actually so stupid. There are some tradeoff between alternative solutions but the difference is not so much. It is not a big failure to select the wrong one. -> You overestimated the failure.
  - Some of the incompetent implementations have some reasonable excuses. -> Your judgement was not fair.
  - Some of the mistakes are real mistakes. -> That is why you are there to contribute the team and the project!

- Don't try to understand everything from day one. There may be thousands lines of code. The stack may be totally new to you. The architecture may be so complicated to understand in a few days. The project may require deep domain knowledge. Nobody expects you to get the everything in a week. Give enough time to yourself. Software is more than the boxes in the white board and it requires huge amount of effort to reveal itself.

- Don't wait for getting everything to get a task. Pick the easiest one and try to do it. For example, adding a new button/endpoint/service/flag which is almost same as one of the existing ones. While doing this task, try to understand which kind of activities you need to finish a task. Implementing production code, implementing unit/integration tests, code review, adding new sections into internal/user documents, doing some configurations in other tools/systems, conducting necessary negotiation with other stakeholders, security scan, bringing it into production (or internal usage) etc. DO NOT rush. Juniors sometimes try to finish the task as soon as possible to show their performance. This is a very common conversation:


        Newbie: Hey. I am done😎Only in a half day.

        Veteran: Hımmm. Did you do X?

        Newbie: Not yet😳

It is ok to make this conversation a few times. However, you should know what to do in order to finish a task after a while. You should be able to finish tasks in every aspects without bothering the other people so much. If your team has a proper, well-written definition of done, you are so lucky :) 

- Every task is an opportunity to learn new things. If you are working in an insightful team, given time for a task includes enough amount of time to increase your knowledge about the project. Don't try to learn everything with a task but ensure every task is a step in your level. You can select one aspect of the task and learn it before delivering the output. For example, goroutines in Go, services in k8s, threads in Python etc. Read documentation about the topic, break somethings to understand the effects, question the design and ask your teammates if there are some open points. Don't ask so much at a time :) I always keep a map in my mind about which topics can be asked to whom and use "Round-robin" to distribute my questions. 

- Invest in your skills and knowledge about the technology stack and the domain. Learn deeply regardless of whether they are necessary for the current tasks or not.  If you are working with Java, be the one of the people who try newest Java version. If you are working with kubernetes, follow the CNCF tools closely. New problems require different perspectives/approaches. You should be one of the "go-to people". In order to achieve this level, sacrifice some of your personal time if necessary. Tasks/roles/companies/projects are temporary. Knowledge and skills are permanent. Do it not only for the project but also for yourself!

- Let's say enough time has passed. You made a progress. You are able to finish given tasks but you are not a part of decision making process effectively. Now, it is time to shadow decision makers. Try to be a part of decision making process even you cannot say anything at the beginning. Join meetings and discussions. Observe decision makers, get concerns, learn parameters, notice "balances". Figure out the anatomy of decision making. Don't forget every decision has some risks and you cannot decide anything without risk. Be brave! Contribute the discussions. Most of the time, the veterans of the project are conservative about giving these kind of responsibilities. Show your capabailities and ask more autonomy. When they see your abilities, they will trust you more.


- Last but not least. Feel responsible from day one. You are a part of the team and you are responsible for every failure and every success.  More or less. If there is a pain in somewhere in your team, feel it in your heart. In companies, there are always some "local people" and "tourists". Tourists are only interested in "fancy" things. They follow their interests. They hate problematic situations. They don't take some much responsibility in case of failure. They always blame decision makers since they never try to be one of them. When they get bored, they just fly to yet another city. In reality, there are lots of limitations and we are not living in a perfect world. Unfortunately they are not aware of this fact. Maybe they are just prefering ignoring it. I don't know. What I want to say be one of the "local people". Local people know not only good places of the city but also the suburb. Local people think about how to manage the city, how to "survive" in the city, how to make the city more beautiful. They can live with the bad sides of the city. At least for a while :) 


These are my approaches as of now. I am sure some of them will change in time. It is a long way... 

<br><br>
Reviewers:

- [Hasan Turken](https://www.linkedin.com/in/turkenh/)
- [Muvaffak Onuş](https://www.linkedin.com/in/mmonus/)

<br><br><br>
**BONUS**

I will be talking about "Being an apprentice software crafter" in April 30th. Here is the details
https://www.meetup.com/Software-Craftsmanship-Turkey/events/260696392
