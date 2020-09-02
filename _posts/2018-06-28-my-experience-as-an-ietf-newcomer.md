---
layout: post
title: "My Experience as an IETF Newcomer"
date: 2018-06-28 00:00:00 -0400
preview: multicastvision.png
tags: ietf networking conference juniper junos
---

I'm William Zhang, and I'm also a graduate of Thomas Jefferson High School for Science and Technology in Alexandria, VA. It feels strange to describe myself as such--I've been introducing myself as a Student Systems Administrator at TJ for the past few years. 

I'm grateful for the opportunities provided to me in my high school journey--primarily the shared responsibility with the other student sysadmins of maintaining the school's critical computing resources--from the school Intranet to the webmail to the networking, virtual machines, and servers in the TJ Computer Systems Lab that make everything run.

Participating in the Student Sysadmins Program has been, without a doubt, the most impactful experience I've had in high school. In a span of less than four years, I went from someone who had nearly no experience with Linux or the open-source software community--the extent to which was booting a LiveCD of Ubuntu on an old desktop--to someone who runs multiple Linux and FreeBSD servers in his basement and Arch Linux on his laptop.

As a student sysadmin, I frequently needed to coordinate with our school's network engineer, Mr. Peter Morasca. It was through Mr. Morasca that I found my way to the IETF.

When Mr. Morasca put me in contact with Lenny Giuliano of Juniper Networks to interest me in a project involving Automatic Multicast Tunneling, I was excited for the opportunity to work on a project where I could collaborate with someone in the networking industry. One thing I had always felt TJ lacked was focus on networking or communications technology. That was understandable; networking is somewhat of a niche subject.

In my observations, I felt that most people at TJ focus on the theoretical side of computer science and/or the applied side of electrical engineering. I've had the privilege of experiencing both sides at TJ. I had tried to take a class on data streams and communications, but it hadn't run for many years due to a lack of interest. All students at TJ are required to work on a capstone research project in their senior year and I still had an interest in networking technology. The research project seemed to be the perfect opportunity to get my foot in the door.

## The Project

With the increasing popularity of high-resolution video streaming, virtual reality, and a growing audience for such content, it is increasingly expensive to distribute this content over the limited bandwidth available to typical end-users, largely as a result of the way content is delivered over the Internet using the unicast delivery mechanism. The multicast delivery mechanism, by contrast, offers significant advantages in requiring far less bandwidth, but has not been widely adopted on the Internet. In order for multicast traffic to be routed to its destination, all layer 3 hops between the origin of the multicast stream and the destination network must maintain a multicast distribution tree and support a multicast routing protocol (typically PIM-SM).

This is where Automatic Multicast Tunneling (AMT), as defined in RFC 7450, comes into play. AMT essentially allows multicast traffic to be encapsulated in a unicast tunnel to devices that do not necessarily have native multicast connectivity to the source. AMT requires two primary components, an AMT relay and an AMT gateway. The relay sits on the edge of a multicast-enabled network and builds an AMT tunnel to the gateway, running on either a network device or an end-host.

The primary goal of the project was to help make multicast adoption more accessible through creating a model for multicast adoption, deploying a public-facing AMT relay at TJ, identifying what multicast content already exists on the multicast-enabled backbone of the Internet (MBONE), and researching current AMT gateway implementations.

TJ is peered with Virginia Tech and has multicast connectivity to Internet2, a high-bandwidth network that interconnects 305 U.S. universities and other educational and governmental institutions and comprises the majority of the MBONE. TJ was therefore a great place to deploy an AMT relay. Thanks to Juniper Network's generous donation of an MX80 router and Lenny's help, the AMT relay was deployed on TJ's border network.

With the AMT relay deployed and working, there was a need for multicast content to make use of the relay. I wrote some scripts to collect logs and statistics on available content on Internet2. In total, I discovered 119 unique multicast sources and 40 multicast groups.

With the content and the AMT relay there, AMT gateway software to receive the content via AMT was also needed. After searching for a while, I was able to find multiple implementations; these were primarily the work of students at the University of Texas at Dallas and Concordia University. The implementations were outdated, so I ultimately decided to use an AMT gateway implementation by Jake Holland at Akamai Technologies that was a relatively recent fork of the work done at UT Dallas.

## Presenting at IETF

It was around this time that Lenny mentioned to me that he was attending IETF 101 in London. As he was a chair of the MBONED working group, he offered to either present the project or find a way for me to participate and present remotely.

I had previously known about the IETF as the organization that created the standards the Internet relies on. As a student sysadmin, I frequently used IRC to communicate with the other sysadmins and alumni, and I eventually found myself bookmarking RFC 2812 to read about the IRC protocol.

Knowing how important the IETF is, I was apprehensive about the idea of presenting at the conference. Even though the presentation would just be within the MBONED working group, I found the idea of presenting in front of professionals in a field I knew relatively little about extremely daunting. What if I said something technically incorrect? What if I explained something that everyone already understood or knew about?

In addition, the MBONED meeting began at 9:30AM GMT, meaning that I needed to wake up early to present at around 5:30AM EDT. With these factors in consideration, I was unsure of whether or not to present the project myself.

I discussed the opportunity with my research advisor, Dr. John Zacharias, to get his opinion--he pointed out that it was a great opportunity to build connections and meet new people in the industry.

I soon realized that it was an opportunity that I would regret not taking. I decided I would present, as it would be, at the very least, good practice for my end-of-year presentation at TJ's annual research symposium, tjSTAR. Lenny helped me modify the presentation I had previously prepared for tjSTAR, condensing it to the portions relevant to the working group. 

In an email, I was invited to register and perform a self-test on the IETF MeetEcho WebRTC remote presentation software to make sure that everything was in order for my presentation.

With that out of the way, I waited with anticipation and nervousness for the morning of March 20th, the day of the MBONED meeting.

The morning of the MBONED meeting, I logged onto the IETF MeetEcho web interface. Once I gave the signal to Lenny, he brought up my slides and I gave my presentation.

With the end of the presentation came the questions period. Jake Holland from Akamai came up to the stand first; one of the AMT gateway implementations I had been looking into was written by Jake, so I was excited to hear from him. Both he and Lucas Pardue from BBC R&D gave helpful suggestions for possible work into delivering multicast content on the web. Lucas suggested WebSockets for delivering UDP content, as WebAssembly does not currently support UDP.

## Conclusion

All in all, my experience bringing my project to and presenting at IETF 101 was a great experience for me. I did feel nervous about presenting in front of industry professionals; but I realized that it was an opportunity that I knew I shouldn't pass up. In the future, I'd love to be able to attend an IETF meeting in person--remotely sharing my research at IETF was a wonderful opportunity, but I look forward to continuing this experience by meeting new people and contributing to interesting projects and new technologies in person.
