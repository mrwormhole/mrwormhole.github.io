---
title: "Why You Should Not Trust Corporate Linux Distros"
date: 2023-08-04T00:00:00+00:00
author: Talha Altinel
description: "Sayonara Red Hat"
tags:
- linux
- opensource
slug: why-you-should-not-trust-corporate-linux-distros
canonicalURL: https://wormholerelays.com/posts/why-you-should-not-trust-corporate-linux-distros
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
draft: false
cover:
  image: https://wormholerelays.com/htukhkizsk.webp
  alt: linux-background
---

## The Great Drama

&nbsp;&nbsp;&nbsp;&nbsp; As you know, Red Hat(actually IBM) decided to shoot itself in the foot again a month ago by closing the RHEL source code leaving downstream community/corporate based linux distros airless and causing great distress to many businesses and many people. If you wanna get a quick summary, I suggest reading [Red Hat strikes a crushing blow against RHEL downstreams](https://www.theregister.com/2023/06/23/red_hat_centos_move/)

As a Fedora user, I have tried to sit back and relax and watch from a distant place to see if there were gonna be a decision to rollback on this decision but unfortunately, Red Hat press kept fueling the fire for a while, I also love to analyze fast decisions in the long term to see if it was really well-thought but I have to say, I was a bit Red Hat biased because of being a micro business owner and seeing many open source small businesses not making enough from what they provide to other people.

After giving my brain relaxed space, I noticed there are so many things wrong with Red Hat from calling people freeloaders to going legal wars against Rocky Linux who allegedly made deals with billion dollars NASA contracts. You may say, well Redhat is a business and legally they can close the source and keep selling their distro while avoiding copycats(extremely wrong to call downstreams copycats, downstream software is as important as upstream software), this was my first thought too from business perspective. But in reality, Red Hat is no longer a trustworthy company and they have gained the habit of shooting themselves in the foot when they have no longer 2 feet to stand on (remembering CentOS's gravestone and empty 10 year commercial support promises)

It was kinda sad for me because Red Hat was the most flourishing company for enterprise Linux. To extend the irony, Oracle and SUSE even went ahead and did retaliation for GPL breaches in legal cases. We do know how Oracle scrutinized open source in the old days with legal court cases against Google after they have become the JVM connoisseur.

I will also suggest watching LearnLinuxTV youtube video to build up why company owned distros like Red Hat are not as reliable as everyone thought;

{{< youtube fqfyM7zE6KM >}}

## My Linux Experience

I am not the best Linux user in the world but I started my journey in 2018 with Ubuntu 18.04 release at uni times. I didn't actually understand its value at that time but it was very nice to download python and other developer related things with a few commands and compile things then if I can help people's issues or questions by learning their projects. 

When I started working at the end of 2019, I started hosting my developed projects in small friendly clouds like Hetzner, Digital Ocean, Linode, Vultr etc. I was actually a heavy heroku user due to having no Linux experience but I realized it is actually more interesting and cost effective to learn Linux for my own servers whether VMs or VPS machines. All of my servers used Ubuntu since 2020.

After windows 10, I kinda started to get windows alergy in terms of machine resources. It was not that bad but I was more into WSL2 debian/ubuntu shells than setting up custom shells like MSYS2 or Git Bash. WSL2 kept me there for a while in windows 10. Then windows 11 released and I was super frustrated with all the bloat software it came with, some softwares were not removable and some were but if you did delete them, they would come in next FORCED update. At the end of 2021, I have gotten rid of all windows machines and installed Fedora 34-35. I was super happy with Gnome 40 versions and I have been hooked to Fedora(simple package manager, dead easy to install) so well.

By March, 2021, I have switched all my server machines from ubuntu to debian
By Aug, 2023, I have switched my local machines from fedora to arch

## Personal Decisions

### Why did I switch from Ubuntu(Canonical) to something else?

I had a personal drama with Canonical, I interviewed for a snap position as a software engineer and they sent me 10 stages of interviews in 2021, luckily I have been eliminated at stage 3 by asking "how do you scan these snap packages for security? what are the regulations for someone who wants to upload random snap packages?" to an engineering manager. He replied "this is confidential corporate information, I can not answer that" like I asked for the password to the vault, Canonical looked like stressful, insincere, untrustable and unhappy people to me, there were also a few controversies from Richard Stallman calling them spyware about the sold Amazon store telemetry data in 2012, however this is no longer the case after a huge community pushback, they no longer have opted-in Amazon store telemetry. I have still a huge grudge against snaps tho. They remind me of Xbox software center of windows10, which comes back everytime with an OS update even if you remove them forcefully, it is fully dictated useless piece of software packing for convenience. And there to ruin your life with auto-updates without your consent.

![Useless](https://media.tenor.com/76tGr9IImPMAAAAd/useless-neil-degrasse-tyson.gif)

### Why did I switch from Fedora(Red Hat) to something else?

This was a tough choice to do as I escaped from one giant to another giant. I have trusted Fedora for 2 years but they have let me down with their stand on open source when they are heavily based on open source itself and also flatpaks I installed recently started crashing all the time to an unbearable point. I am looking at you postman flatpak. Some may say this is extremely emotional argument and I would say it is personal like my Canonical choice. I as a person have seen enough injustice and unethical behaviour until this age, so screwing up hard-working people from RockyLinux/AlmaLinux/insert X distro/OracleLinux(even them) is a big no for me. And not keeping their promises is also a huge letdown for any of Red Hat products. Also on top of that, calling their actual users freeloaders and trying to short-circuit open-source software supply chain around them?

![Shotgun](https://media.tenor.com/k9f1p3CZiksAAAAC/gun-db-shotgun.gif)

## Rational Decisions about Corporate Linux Distros

So let's visit rational decisions, There is a euphoria of consuming something that is prepared by a giant company or a brand because of giant marketing. And lots of people trust giant companies more than small companies or small communities. Why? well they think more people are more successful in creating products. This is a myth, there is even a book called "The mythical man-month" written by Frederick P. Brooks Jr. which explains this myth very well, I highly recommend checking this book out, essentially more people means more complexity to managing efficiency and productivity, people are not honey bees where you increase the count and get the best quality and large quantity of honey. This is why big companies have a higher reputation for their products than a normal unknown small company/community which does the same thing.

Unfortunately, when your business becomes the biggest and the most reputable contender, they can do anything they want and get more reluctant about what customers/clients think. Maybe exceptions like Amazon exists, but let's not get there as it is a different topic.

I do think some Linux distros of corporates are organized well and near amazing but the trust is what matters at the end of the day. I also don't believe everyone in Red Hat or Canonical is evil people, they are just a bunch of peeps trying to make a buck for their day. It is those evil managers with extreme greed who chain the wrong command sequence to the higher level and screw that reputation down to 0 trust. I am looking at you IBM people.

I still recommend beginner friendly distros like Ubuntu, Fedora to people who are dead new to Linux desktops like very rookie non-dev people. But if you are serious/experienced people, you can cut the middleman and go fully community based distro like Debian, Arch. It is also important to remember and remind the history of these corporations to new beginners so they can decide on their own fate.

At the end of the day, Linux and GNU didn't come out as easily as one giant corporation product, they were fully community based and community funded. It took everything including web servers, market share, developers, operations by storm and poured out tons of jobs and productivity gains. If you have 10000 people using a community product, a person can sky-rocket the product(the distro or the open source project) by giving just as small as $1 or filling issues or fixing bugs to gain intellectual joy or pay back for the productivity they gained from.

I also run a micro hosting business so I don't like non-trustable or non-predictable greedy companies to work with. If they dump the distro, you are on your own to migrate to a new distro with existing hosted software workloads which is not FUN at all.

## Open Source Philosophy

Some companies unfortunately don't like give-take motion, it is always take take, take forever. They die and be gone then what they have achieved was a meaningless grind with high digits amount of cash. I believe in balanced open source philosophy where people give and take at the same time. The most successful innovative projects have been this way plus there is always something you can open source like an indie project or noncommercial fun project that is gonna be out there when you die one day. It could be in docs, issues form, doesn't even have to be the code.

Using a community distro means you are highly intellectual user with a goal in mind to aid the community and amplify their voice. Not every normal user may wanna go down this path as you sometimes will be checking mail lists, forums and VCS issues like instragram. But you can also just be an user as linux community disros are getting easier to use every day. I am looking at you archinstall(the best thing I have seen for arch linux desktop users) script and yes debian server installation is still dead confusing :)

## Then why you should use and devote time(if you wish) to community distros?

![Arch](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8lw5ib75eyy4joa2b4j1.png)
![Debian](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/u6dlwl9slil0g1b6re2l.png)

- Drama Free
- No middleman, you don't depend on anyone's very dumb corporate decisions
- Joyful learning cycle for the curious mind and always people there to help you out
- Freedom to do anything you want without being in drama circle
- Freedom to brick your operating system
- Freedom to hang out on issues forums or bug forums to give an insight of what you have seen or how you have solved
- You don't like take, take, take materialistic cycle; you love to produce ideas and give something back sometimes
- You like to feel like attending religious communities(church, mosque, synagogue) who embraces but you hate cults and politics

> “Sometimes it is the people who no one imagines anything of, who do the things that no one can imagine.”
>
> — <cite>Alan Turing</cite>