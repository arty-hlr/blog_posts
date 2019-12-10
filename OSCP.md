So, first blog post had to be about OSCP even if I passed it two months ago, or my buddies in the Infosec Prep discord server would be mad! So here we go, short(ish) review of PWK (the course), OSCP (the exam), what preparation I had, a few pieces of advice, and what's next!

![](https://raw.githubusercontent.com/arty-hlr/blog_posts/master/images/oscp_logo.png)

## Background and preparation before

I started getting in the infosec world in January 2018, coming from a mathematics background with almost no programming experience. Along the way I picked up good Linux knowledge, C, python, and did quite a few CTFs. So that was me before the summer 2019, some CTF experience, some programming experience, but no "hacking a machine" experience.

I didn't have in mind at all to go through OSCP this year back during the summer, but I got started on HTB anyway, and got to pro hacker rank by August, so I had 10 HTB machines, mostly active), under my belt by the end of August, thanks to [Ippsec](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) basically, and also thanks to the huge community on HTB's official Discord server who gave me tips and nudges when needed.

So that was my "preparation", between quotes because even mid-August I had no idea I was gonna be signing up for OSCP in a few weeks time! But well at that point in time I was talking with a recruiter on Discord who said that he would have jobs for me in Europe with OSCP, so that got me thinking, got me motivated to try my hand at the huge beast I thought OSCP was, realized I had one month free before the start of my university year, so I gathered why not, and on the 8th of September I received my access to PWK!

## First steps in PWK labs

Sunday morning after receiving the VPN access I went straight into the labs while browsing through the PDF quickly, I was fairly comfortable with most subjects and I wanted to get a taste of the labs. I wasn't disappointed at first, got a list of all hosts' IPs, and started playing with the first one on the list, which went ok as it was fairly easy, but isn't what I'd recommend really. We have a bot on the Infosec Prep server that tells how difficult a machine is and whether or not it has dependencies, meaning machines you should root before, and I relied on it to see which machine I wanted to try after, but the best idea would have been to do a sweep of all hosts looking for low-hanging fruits like Samba and other ports holding usually vulnerable services.

I was going full-time on PWK, not having anything else to do with uni, so I must have spent around 8-9 hours a day in the labs the first two weeks, and rooted about 10 boxes a week (not that numbers matter anyway actually). I had great fun, even if some machines were pretty frustrating (when you see some bad documentation in French in the labs, you'll understand), but it was ok, and having the community being there too was great. Did I mention that I started the labs along with two other guys from that discord server? Having people around who are doing it at the same time as you are is great, creates bonds on a period of 1-2 months of constant daily messages!

## Getting tired of the labs

Two weeks in, I was getting tired of the PWK labs to be honest, and I won't hide that. Some machines were easy as common credentials, all are old a\* f\*\*\*, so old that kernel exploits work on a lot of them, but you'd have to compile them on old VMs, which was a pain. Maybe I should have looked more at privilege escalation routes that didn't use kernel exploits, more than the few times I did, but I was growing tired of the age of the lab and how some machines were really buggy sometimes. I was not looking forward to the next machine on my to-do list, and I needed something fresh. I was longing to go back to HTB as well, since I had focused solely on PWK for the last two weeks, so I decided to go back there and take a break from the PWK labs.

![Back to HTB!](https://raw.githubusercontent.com/arty-hlr/blog_posts/master/images/oscp_htb_logo.png)

That was probably one of the best decisions I've made during my preparation for the exam, taking a break from PWK and working on other sources and other stuff more relevant to the exam. I knew pivoting was part of PWK for example, and I did try that a bit, but it didn't interest me that much and I knew I wouldn't have to pivot in the exam, so scratch that. Instead I took the list of OSCP-like HTB machines, and went through that.

![List of OSCP-like machines](https://raw.githubusercontent.com/arty-hlr/blog_posts/master/images/oscp_htb_machines.jpg)

That got me motivated again, and it taught me a lot as well. One thing I should emphasize is that I was taking detailed notes in markdown for each machine to have and go through later, and as a preparation for the exam report. I didn't do the exercises, so no lab report for me (not worth it anyway, going through all the exercises and document them, and documenting 10 machines that have different types of vulns is way too much work for a poor 5 points in my opinion and prepping you for failing on the exam, more on that later), so I could focus on hacking machines.

## Focusing on weak points

At that 3 weeks/one month point, I made a list of what I was good at, and what I wasn't so good at compared to the PWK syllabus, and decided to take a break from hacking machines and instead work more specifically on my weak points, which were SQL injections, Windows in general, and privilege escalation on Windows. So I worked on [Portswigger](https://portswigger.net/web-security)'s SQL labs, which helped a lot, and downloaded the [lpeworkshop](https://github.com/sagishahar/lpeworkshop), which was a great decision. The slides and PDFs are quite dry and usually just show what the vulnerability is and how to exploit it, and sometimes even with GUI tools, so I decided to take it as a lab machine, and make notes on how to find and exploit each vulnerability lab style, that means from a reverse shell that I would have gotten from another exploit. That was very rewarding work, even if it took some time (as I'm nice [here](https://github.com/arty-hlr/privesc_notes) are those notes) and I learned a lot especially about Windows. When tib3rius from the Infosec Prep server announced his Linux privesc Udemy course, I got it as well as I thought it would be helpful in going through everything again, and it definitely was, and not expensive at all, [here](https://www.udemy.com/course/linux-privilege-escalation/)'s the link to it.

It was around that time as well that I tried out tib3rius' [autorecon](https://github.com/Tib3rius/AutoRecon) tool to automate the initial enumeration phase of the exam. I wasn't convinced at all at the start, but it was a wise decision to use it as it's very helpful to speed up the enumeration part, just launch it, let it work and do the things you would have done anyway like gobuster or nikto, and when it's done just go through its results. For situations like OSCP when time is counted and you can do something useful during the time autorecon takes, definitely very useful.

It's also around that time that I decided to schedule my exam, I knew spots were booked early, so I wanted to have mine well before exam time. I booked a Wednesday mid-October at 7 am, I wanted to be fresh and not waste waking hours not on the exam, and I was clearly going in with the idea that I would do an all-nighter and work on the exam the full 24 hours.

## Working on the buffer overflow

I had pushed back working on the buffer overflow (BOF for short) because that was something I had done in the past thanks to Jon Erickon's [book](https://nostarch.com/hacking2.htm) `Hacking, the Art of Exploitation`, and I knew I just needed to get used to it on Windows and above all get familiar and comfortable with Immunity. That took a bit of time, but it was great discovering Immunity and mona (DO read its [documentation](https://www.corelan.be/index.php/2011/07/14/mona-py-the-manual/), great tool, learn to use it), and I wrote a skeleton script for the exam BOF to gain some time. I basically just had to comment and uncomment code and go through it step by step, and it saved me a lot of time writing code on the exam (I'm nice, but providing it to you wouldn't help you really, instead just do what I did and write your own!). Here are some resources I used for the BOF preparation:

+ [dostackoverflowgood](https://github.com/justinsteven/dostackbufferoverflowgood) (very basic though)
+ [vulnserver](https://github.com/stephenbradshaw/vulnserver)'s TRUN
+ SLMail (described in the PWK PDF)

Once that BOF practice was done I felt a bit more ready for the exam, at least I knew I had covered every point in the syllabus.

## Time for a mockup exam

That was an idea I had as I wasn't feeling really ready for OSCP yet and I had around 1-2 weeks left before the actual exam, so I decided to take a few VulnHub and HTB machines along with a BOF, and see if in two days, so 2x12 hours, I could get them. I didn't choose good machines for mine, as two of them were too easy, and two of them were too hard, so I won't be recommending them, but the BOF I used (FreeFloatFTP) made me confident my skeleton script was ok, and it made me get used to OBS for recording, taking screenshots, and logging my terminals with tmux (here's the [plugin](https://github.com/tmux-plugins/tmux-logging)). So overall a good prep at least logistically, and it made me realize I was missing a tool as well, and obviously I learned some from the two harder boxes as well, so it wasn't time wasted anyway.

Basically just choose 4 machines randomly from easy/medium machines from VulnHub/HTB plus one vanilla BOF, and you'll be fine.

## Exam day!

Wednesday came, and with it the OSCP exam. I had gone to bed early the day before to be fresh on D-day, and I had set up everything I needed for the exam. I woke up at 6:30 am, had the verification steps with the proctor at 6:45, prepared breakfast, then finished eating it while reading through the instructions and launching autorecon for the enumeration. I then got to work on the BOF, and by 7:45 am I got it along with my first 25 points!

From then I focused on the 20 and 25 pointers, and shell by shell by 11 am I had enough points for passing, which surprised me a lot. I was only lacking the privesc on the 25 pointer, so that made around 85 points secured. I then had a break, a nice lunch, and began working on the report as I couldn't get anything from that 25 pointer. I went through my OBS recordings, gathered screenshots, and made sure I had everything I needed before killing the VPN connection.

Overall the exam was really fun, way easier than I expected (so I didn't have to pull an all-nighter), and the proctors were nice, even though they use messages when you announce you take a break or come back from one that sound like they're from a premade list haha.

I went to bed that evening confident I had everything for the report, and thankful the exam was over!

Thursday was report writing, I used markdown for my report (obviously was writing it in vim) and [that](https://github.com/noraj/OSCP-Exam-Report-Template-Markdown) template, which was really easy to use. Compiling the report to PDF with pandoc was a breeze, and I loved not having to write it in MS Office or OpenOffice, that was a nice but long experience. By evening I had finished it with around 40 pages, read it over 3 (three) times (and glad I did because one of my proof screenshots was missing something!) and I uploaded it and sent the email with the link on that Thursday evening!

## Good news!

I was making myself not check my emails every ten minutes as I knew it would take around 10 (ten is a lot when you're waiting for the result haha) business days for them to come back to me with the results. Which is why it was a very nice surprise on the Saturday morning, so barely 36 hours after having submitted my report, to get *that* email announcing I had passed!

## Conclusion

Overall it was a very fun month and half preparing for OSCP, and it definitely helped in the job searching I've done since with a recruiter, but I'd only half-heartedly recommend it. It is definitely outdated, and I learned more from HTB than I did from the PWK labs, that's for sure, even though people will argue that no matter how old the machines, the point is to get the methodology, that's not completely true in my opinion, and getting exposure to more recent OSes is good, be it only to have to use Powershell more, and kill the fact that all the machines or almost are vulnerable to kernel exploits due to their age.

One other thing to underline is that OSCP is missing a big part of what was useful when I took the technical tests later for interviews of positions as a junior pentester, namely focus on web attacks. There maybe a few sentences on SQLi and XSS in the PDF, but the focus is on finding public exploits, not on exploiting XSS in a website, which was exactly what was asked of me in most technical tests. 

To conclude, I'd say OSCP is very fun although outdated, and **is** recognized by companies, recruiters and HR, so those four letters on your CV *will* help you get interviews for penetration tester positions, but as real preparation for a pentester position, I feel eLearnSecurity WAPT is much more adapted, being full web black box attacks. So choose OSCP for the recognition, but choose other certs like eLearnSecurity, or just learn on the internet, for knowledge.

## What's next

I'm pretty lucky to have found a position as a security consultant with a focus on IoT reversing within one month of looking for a job (thanks OSCP and above all that awesome recruiter I was working with!), and I decided in early December before getting that offer to take on XDS an WAPT from eLearnSecurity, so there's that for the next few months, plus starting with the job, so I'll be pretty busy. I don't plan on taking any other OffSec certs in the near future as OSCE is really outdated, OSWE is code reviewing in languages I'm not familiar with, but I'm looking forward to get more courses options from work, with probably some about ARM exploitaiton and more generally IoT security, and maybe one day SANS 760, who knows!


## Some parting advice

+ Go for low-hanging fruits by doing a full hosts scan in the PWK labs
+ Use HTB and other labs as well
+ Don't do the lab report (unless you have time to kill)
+ Use autorecon in the exam while doing the BOF
+ Do a mockup exam to get used to the format and logistics of the exam
+ Manage your time well in the exam

