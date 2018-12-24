
# Behind the Scenes: Challenges of Organizing HackPrinceton

If you’ve ever attended a hackathon, you know how fun working on projects and learning in a fast-paced environment can be. There’s tons of technical workshops, speakers, mentors, great people, free food, energy drinks, hardware, and fun events. Maybe a bit of sleep too :)

A lot of work goes behind organizing a large hackathon like HackPrinceton, but not all of the work is “techy”. We grapple with many interesting logistical, communication, and management challenges, and I hope to give a glimpse of what some of those entail. Optimizing solutions to these problems has been among the most fun and rewarding things I’ve had the opportunity to do in college. Describing everything we work on would take unreasonably long, so I’ve selected a couple for variety.

### **Acceptances**

In a perfect world, everyone interested in HackPrinceton would be able to attend. However, in practice, building space, technical resources, and money are limited, so selectivity is unfortunately unavoidable. We typically receive between 3 and 4x more applications than people who can attend. Each application consists of a resume, short essay response (to one of several prompts), demographic information, and general project interests. Resumes and short responses are each blind graded multiple times. How do we best decide who should be able to attend from limited information?

It partly depends on what the objective function is. For us, it’s optimizing for an environment where hackers are bold, eager to learn, persistent, outgoing, and creative. We would like every participant to walk away from the weekend having learned something new, built something they’re proud of, and made new friends. It’s unclear what the global solution to this optimization problem is.

A straightforward quantitative approach would be to simply accept x applicants with the highest composite essay and resume scores. However, such an approach would fail to guarantee geographic, racial, gender, and field of study diversity, as well as prevent first-time hackers from participating. Diversity is something we actively strive for, both because it enhances the perspectives of event participants, and because it directly aligns with our mission to make technology more accessible.

We also have to ensure that the total travel reimbursement allocated to accepted hackers falls within budget, that our bus routes (we typically send buses to various schools) are completely filled, and that there are enough experienced hackers to drive forward projects that sponsors will enjoy seeing. Algorithms help balance these interests and maximize fairness, to the extent that it is possible.

### **Hosting**

Due to Princeton’s fire code regulations, hackers are not allowed to sleep overnight in any non-residential building, despite this being the norm at virtually every other hackathon. This means that we have to match hackers with students on campus who have space in their rooms, and are willing to welcome a guest.

As you might guess, this is a complex problem to solve by hand. Not everyone is comfortable living with people of a different gender, and not every host has the same number of vacant spaces in their rooms. Furthermore, some hosts may only be available on Friday night or Saturday night, but not both, and some hosts may have specific bedtimes to honor.

We explored our approach to this problem in [part 1](https://medium.com/hackprinceton/lessons-learned-from-hackprincetons-host-matching-part-1-ad3cf1ef7e24) of a Medium article series, but the premise is that host matching reduces to a modified maximum flow problem. Our algorithm generates optimal matchings using as few hosts as possible, but it still needs a minimum number of hosts available in order for each hacker to receive a room.

In practice, designing and executing *incentive-compatible *mechanisms to encourage Princeton students to host, has been difficult. The incentive needs to scale with the number of guests; large rooms should feel motivated to host as many students as possible. But smaller rooms should also feel the reward of hosting a single student is high enough for them to host. Most rooms on campus are, after all, singles and doubles.

### **Judging**

Judging happens after 36 hours of hacking and is one of the most anticipated parts of the event, as participants get to show off their new creations. How do we ensure that every project gets visited by an equal number of judges, and how do we ensure that the bias of any particular judge doesn’t unfairly impact a given project’s ability to succeed?

Numerical ratings are ubiquitous, but they are flawed for a number of reasons. How do you get different people to agree on what a 5/10 is? What if one project receives an exceptionally high score from one judge, and an exceptionally low score from another judge? What if a judge starts off with a certain set of standards, but those standards fluctuate as the judge views more projects? Normalizing scores helps reduce inconsistency but it still isn’t fair, because genuinely good projects may get a lower score than otherwise, and genuinely bad projects may get boosted. The problem at hand is that judges only have a *local view o*f all projects, and without a global view, it is hard to design a numerical rating system that is fair, consistent, and independent of the order in which entries are judged.

We use a popular open-source system from MIT called [Gavel](https://github.com/anishathalye/gavel), which utilizes pairwise comparisons to rank projects. The intuition is that people are much better at deciding whether something is better in *relative* terms, rather than assigning a quantitative score. For example, it is easier to say whether dogs are better than cats, rather than rank dogs on a scale of 1–10. Gavel then implements some math to translate pairwise comparisons into a global ranking. We have found Gavel to be a satisfactory solution, provided we have enough judges to perform all pairwise comparisons in timely fashion. Finding enough judges is another interesting problem in itself, but one that I won’t discuss today.

### **Predicting Attendance**

Having a good handle on how many people are expected to show up is critical, as we cater six free meals, give free swag to all attendees, and staff a hardware lab. Ensuring that there is enough food and swag is especially imperative, since most hackers travel very far to get to Princeton — sometimes from another country. Typically, about 70% of accepted non-Princeton hackers who confirm their intent to attend, actually end up attending. Travel reimbursements serve as a fairly strong incentive for non-Princeton hackers to commit to participating, because travel reimbursements are redeemable only in person at a specific time during the weekend.

Running a linear regression yields a reasonable prediction for non-Princeton attendance. However, Princeton students present an interesting oddball. We cannot cap the registration of Princeton students because by university policy, all students must be allowed access to the event. Thus, online registration must remain open until the start of the event, which means that Princeton students could choose to show up last minute. If more Princeton students than anticipated show up (which is great), we are forced to prioritize food and swag for outside hackers who have traveled further to attend, and because Princeton students usually have some dining option on campus.

### **Logistical Problems**

There are many moving parts closer to the event, so these challenges are mostly about optimizing time and manpower allocation, as well as having good contingency plans.

*Moving stuff from storage*:

Due to lack of space on campus, we are forced to store items in an off-campus location called the Entrepreneurship Hub. We also receive many shipments in the weeks before the hackathon, such as prizes, company swag, and other materials. Moving things by foot to Friend Center, where the hackathon is held, is infeasible. We typically rent a van the night before to move our belongings, and it takes 10–15 full van loads to finish the operation. We then have to do the reverse after the event during cleanup.

*Buses:*

Once hackers have been selected, we have to coordinate bus routes for the most popular schools. Each bus route needs to be within budget, and efficient / guaranteed to completely fill. Upon deciding on a bus company and routes, we have to setup a first-come-first-serve ticketing system, enact policy to handle last minute cancellations and walk-on bus attendees, and make sure that the bus company is in sync with our timeline. Sometimes the bus arrives late, and opening ceremony is thus postponed.

*Hardware Lab:*

We use the electrical engineering undergraduate lab and give hackers access to a variety of hardware and heavy machinery, such as laser cutters. This space needs to be supervised 24/7, and there needs to be a robust check-in/out system for hardware, or else we lose a lot of money.

### **Leadership and Team Management**

We have an organizing team of 30 undergraduate students. With a team so large, it can be difficult to ensure that everyone is happy, and that no one falls through the cracks. How do we ensure that everyone feels they have complete ownership over their responsibilities? How do we ensure that every person is convinced by and committed to the organization’s vision? How do we foster a social environment where relationships develop beyond the scope of our work running HackPrinceton?

Everyone’s schedules are different and constantly changing, and sometimes personal timelines block the progress of the event. When is it appropriate to step in to help someone finish a task, and when is it appropriate to delay the event timeline in order to preserve someone’s autonomy?

### **Improvisation**

No matter how much planning is done, something inevitably goes wrong, and with enough experience, organizers can quickly converge on an effective solution. Perhaps snacks run out quicker than expected and Costco is closed for the night, or a hacker’s host doesn’t show up. Maybe a sponsor doesn’t get enough project submissions to their prize category, or visits to their workshop, and demands their money back. Maybe public safety forgot to unlock the doors, and hackers are stranded outdoors in sub-freezing weather. Maybe a judge cancels last minute, and there aren’t enough judges to finish judging on time. Murphy’s Law reliably holds :)

While these are challenges that we work on as HackPrinceton organizers, the general nature of these problems are typical of running any large organization. If solving interdisciplinary problems while building a community sounds appealing, consider forming a new conference, hackathon, or other event at your school or workplace! It will be worth the effort.

[*HackPrinceton Fall 2018](http://www.hackprinceton.com), our 15th edition, is coming up on **November 9–11, 2018**. If you are a Princeton student, registration is still open at [my.hackprinceton.com](http://my.hackprinceton.com). We have an exciting lineup of speakers, workshops, and sponsoring companies to commemorate our 8th year, and hope to see you there!*

![](https://cdn-images-1.medium.com/max/3400/1*sR0oqZ1KdbcmQHRHA8y3_A.jpeg)
