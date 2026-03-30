---
title: Running FediFetcher from a Synology NAS
description: ""
date: 2023-06-12T01:24:23.025Z
preview: ""
draft: false
tags:
  - DSM
  - FediFetcher
  - MastoAdmin
  - Mastodon
  - Synology
  - SingleUserInstance
categories:
  - Social
keywords:
  - DSM
  - FediFetcher
  - MastoAdmin
  - Mastodon
  - Synology
  - SingleUserInterface
---


## Intro

I wrote about getting my dedicated Mastodon instance/server up and running (thanks [Masto.Host](https://masto.host)) [the other day](), and one key tool I am using is [FediFetcher](https://github.com/nanos/FediFetcher) to pull posts from folks I follow, threads I interact with, etc. I started by relying on GitHub Actions, but I've found that GitHub is throttling my pipeline runs (which is understandable as I'm not paying for it!).

I [exchanged some toots](https://cdrum.social/@michael@thms.uk/110518440880306802) with the [author](https://mstdn.thms.uk/@michael) of FediFetcher, and even he says he eventually migrated FediFetcher over to his infra.

[![](/Post%20from%20@michael@thms.uk.png)](https://cdrum.social/@michael@thms.uk/110518440880306802)

I've got a Synology NAS sitting at home that is probably pretty underutilized. I know I can run docker containers (and normal shell scripts) on it so I figured I'd do that for a while and see.

In the README for FediFetcher, a Docker Container is available at [ghcr.io/nanos/fedifetcher:latest](https://ghcr.io/nanos/fedifetcher:latest). See [https://github.com/nanos/FediFetcher/pkgs/container/fedifetcher](https://github.com/nanos/FediFetcher/pkgs/container/fedifetcher).

I first thought I could use DSM's GUI to install the image/container, but I was getting errors when trying to add the repo url. After a search online, I found this Stack Overflow [thread](https://stackoverflow.com/questions/73825556/adding-ghcr-github-docker-regustry-to-synology-docker-results-in-registry-ret) that says aparently GitHub container repository isn't supported in DSM? Huh?

The comment in question:

[![](/2023-06-11_18-41-04.png)](https://stackoverflow.com/questions/73825556/adding-ghcr-github-docker-regustry-to-synology-docker-results-in-registry-ret)

I eventuyally got the docker image downloaded, but I couldn't figure out how to get the parameters passed into the docker container through the GUI. You can do it from the commandline, but I thought, if I was going to move everything to the terminal/cli, I might as well just run it as a normal script via cron.

So, that is what I decided to do.

---

## Synology DSM Terminal

First requirement is you need to have terminal (ssh) access enabled on your NAS. You can see [this article](https://kb.synology.com/en-global/DSM/tutorial/How_to_login_to_DSM_with_root_permission_via_SSH_Telnet) to get that turned on. 

***I do not use the root/admin user, ever.***

### Create a SSH public key if you don't have one already and add it to your GitHub Account

```sh
cdrum@DrumCloud:~/Mastodon$  ssh-keygen -o
Generating public/private rsa key pair.
Enter file in which to save the key (/var/services/homes/cdrum/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /var/services/homes/cdrum/.ssh/id_rsa
Your public key has been saved in /var/services/homes/cdrum/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:c60xxxxxxxxxxxxxxxxxxxxxxxxxxQ (I obfuscated this on purpose)

cdrum@DrumCloud:~/Mastodon$ cat ~/.ssh/id_rsa.pub 
ssh-rsa AAAAB3Nzaxxxxxxxxxxxx
```

Add the above string into your SSH Keys in GitHub

### Clone the FediFetcher Repository and Configure

I created a folder "Mastodon" in my user's folder to keep things together as I plan to do more mastodon stuff on the Synology.

```sh
cdrum@DrumCloud:~$ mkdir Mastodon
cdrum@DrumCloud:~$ cd Mastodon/
cdrum@DrumCloud:~/Mastodon$
```

Then clone: `git clone git@github.com:nanos/FediFetcher.git`

Now in order to install the requirements, I first needed `pip`. This worked for me: `python -m ensurepip --upgrade`

Make sure it's in your `PATH`, I did this: `echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.profile` and logged out and back in.

Now, I can install the requirements: 

```sh
cdrum@DrumCloud:~/Mastodon/FediFetcher$ pip3 install -r requirements.txt
Defaulting to user installation because normal site-packages is not writeable
Collecting certifi==2022.12.7
  Downloading certifi-2022.12.7-py3-none-any.whl (155 kB)
Collecting charset-normalizer==3.0.1
  Downloading charset_normalizer-3.0.1-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (195 kB)
Collecting docutils==0.19
  Downloading docutils-0.19-py3-none-any.whl (570 kB)
Collecting idna==3.4
  Downloading idna-3.4-py3-none-any.whl (61 kB) 
Collecting requests==2.28.2
  Downloading requests-2.28.2-py3-none-any.whl (62 kB)
Collecting six==1.16.0
  Downloading six-1.16.0-py2.py3-none-any.whl (11 kB)
Collecting urllib3==1.26.14
  Downloading urllib3-1.26.14-py2.py3-none-any.whl (140 kB)
Collecting python-dateutil==2.8.2
  Downloading python_dateutil-2.8.2-py2.py3-none-any.whl (247 kB)
Installing collected packages: urllib3, six, idna, charset-normalizer, certifi, requests, python-dateutil, docutils
Successfully installed certifi-2022.12.7 charset-normalizer-3.0.1 docutils-0.19 idna-3.4 python-dateutil-2.8.2 requests-2.28.2 six-1.16.0 urllib3-1.26.14
```

I created a handy shell script to run this python script with my parameters. We'll use this later in the crons. I named the file `fedifetcher-cron.sh` and put it outside of the FediFetcher folder in case I do any github pulls - I don't want it to be overwritted/destroyed. In this script, I run `python find_posts.py` with the parameters above. Here's what the script looks like:

```sh
cd ~/Mastodon/FediFetcher
/bin/python /var/services/homes/cdrum/Mastodon/FediFetcher/find_posts.py --access-token=YXYXYXYXYXYXYXYXYYX \
--server=cdrum.social \
--home-timeline-length=200 \
--max-followings=80 \
--max-followers=80 \
--max-favourites=40 \
--max-bookmarks=80 \
--from-notifications=1 \
--http-timeout=10 \
--lock-hours=1
```

Note I added `cd ~/Mastodon/FediFetcher` at the beginning so the `artifacts` folder could be found.

### Manual Runs

Running it, do note that the initial load will take quite some time!

```sh
2023-06-11 23:18:05.612588 +08: Added context url https://weedyverse.de/@admin/109672213741887736
2023-06-11 23:18:06.373872 +08: Added context url https://toot.matereal.eu/@schmittlauch/109564384452007393
2023-06-11 23:18:07.249928 +08: Added context url https://c3d2.social/@astro/109708783616591498
2023-06-11 23:18:07.250256 +08: Added 84 new context toots (with 6 failures)
2023-06-11 23:18:07.339140 +08: Processing finished in 3:27:57.552086.
cdrum@DrumCloud:~/Mastodon/FediFetcher$ 

```

It didn't take as long as I thought since I used to run the script via a GitHub Action, so maybe not as muc catch up to do? 🤷‍♂️

I proceeded to run a few times manually. 2nd run lasted about 45 minutes. Third time about 21 minutes. 

### Configure the CRON (via Task Scheduler)

The [example shell script](https://github.com/nanos/FediFetcher/blob/main/examples/FediFetcher.sh) suggests running it every 10 minutes, so that is what I'll do. I decided to add the cron to the user's account, as opposed to in the `/etc/crontab` since it doesn't need to be run as root.

I found the command `crontab` wasn't avaialble to my user through the terminal. Perhaps it's not installed or restricted. I'm to lazy to investigate, frankly. I decided to use the Synology's GUI "[Task Scheduler](https://kb.synology.com/en-global/DSM/help/DSM/AdminCenter/system_taskscheduler?version=7)" instead (as it's effectively cron anyway!).

First thing to do is note down the full path of the script. In my case it is: `/var/services/homes/cdrum/Mastodon/fedifetcher-cron.sh`

From the Synology DSM interface, find **Control Panel**, and then **Task Scheduler**.

![](/2023-06-12_12-06-15.png)

From **Task Scheduler**, I selected **Create**, then **Scheduled Task** then **User-defined script**.

![](/2023-06-12_12-06-27.png)

First screen, name it. 

![](/2023-06-12_12-13-09.png)

Then in **Schedule**, specify a schedule. I chose to run it every 10 minutes as that's the recommendation in the example script. The documentation states there's a lock file to ensure the script isn't run more than once in parallel in case the script takes longer than 10 minutes to run, which it likely will from time to time.

![](/2023-06-12_12-13-35.png)

Lastly, in **Task Settings**, I chose to have it email me after each run (for the short term) so I know it's running. I'll disable this feature going forward, or at least send me the email when there's an error.

In the **User-defined script**, this is where we put in the full path to the script we want to be run.

![](/2023-06-12_12-14-14.png)

And that's it! Click OK, and wait to see if it runs!

## Closing thoughts

I think the script is great as I am not super keen on adding relays. But I will not rule out relays completely. I'll want to see how this goes over time. One issue with not using relays is my own toots won't federate to servers where local users don't follow me.. So that's a concern that's in the back of my mind. But for the time being, this is a good solution for populating relevant threads to me. 

I'll report back!