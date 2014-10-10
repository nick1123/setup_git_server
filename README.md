# How I Setup a git server

Setup a git server (inspired from 2 sources)
* http://git-scm.com/book/en/Git-on-the-Server-Setting-Up-the-Server
* https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-git-server-on-a-vps

First steps, login onto your Ubuntu server, and then

    sudo adduser git
    sudo su git
    cd
    mkdir .ssh
    vim ~/.ssh/authorized_keys # add my pub key

Key-based SSH authentication usually enforces security by requiring restricted rights on the involved files. To prevent SSH from refusing to work, type this:

    chmod -R go= ~/.ssh

Now, you can set up an empty repository for your users by running git init with the --bare option, which initializes the repository without a working directory:

    cd ~
    git --bare init notes.git


As an extra precaution, you can easily restrict the 'git' user to only doing Git activities with a limited shell tool called git-shell that comes with Git. If you set this as your 'git' user’s login shell, then the 'git' user can’t have normal shell access to your server. To use this, specify git-shell instead of bash or csh for your user’s login shell. To do so, you’ll likely have to edit your /etc/passwd file:

    exit
    which git-shell
    sudo vim /etc/passwd
    # At the bottom, you should find a line that looks something like this:
    # git:x:1000:1000::/home/git:/bin/sh
    # Change /bin/sh to /usr/bin/git-shell (or run which git-shell to see where it’s installed). The line should look something like this:
    # git:x:1000:1000::/home/git:/usr/bin/git-shell

Now, the 'git' user can only use the SSH connection to push and pull Git repositories and can’t shell onto the machine from your Mac/Windows machine. If you try, you’ll see a login rejection like this:

    ssh git@YOUR_IP_ADDRESS
    fatal: What do you think I am? A shell?
    Connection to gitserver closed.

Setup a repo called notes

    mkdir ~/dev/notes
    cd ~/dev/notes
    git init
    touch bob.txt
    git add .
    git commit -m 'initial commit'
    git remote add origin git@YOUR_IP_ADDRESS:/home/git/notes.git
    git push origin master

In a tmp dir on your Mac/Windows make sure you can clone the new notes repo

    cd /tmp
    git clone git@YOUR_IP_ADDRESS:/home/git/notes.git

That's it!
