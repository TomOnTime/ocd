## Track dotfiles in git

I got tired of having my common dotfiles (.bashrc, .pythonrc, .vimrc, etc.) out of sync across all the different workstations and shells I use on a regular basis. So, I rewrote them in a way to be generic, allowing host-specific and domain-specific files to be sourced as appropriate. I also included window-manager specifics, like my Openbox configuration. This was inspired by a
[http://books.google.com/books?id=mKgomQz5KH0C&pg=PA149&lpg=PA149&dq=flickenger+movein&oi=book_result&resnum=1&ct=result#v=onepage&q&f=false](similar
approach) I read a very long time ago.

Now I can take a freshly installed operating system and make it cozy and
customized without any tedious repetition. I also get the added benefit
of source control to view previous versions of files.

When I set up a new box, first I install my private SSH key:

    mkdir -p ~/.ssh
    scp user@someotherhost:.ssh/id\.\* ~/.ssh

With the appropriate github SSH identity in place, I can do this:

    which git || sudo apt-get install git-core
    git clone git@github.com:obeyeater/ocd.git ~/.ocd
    ~/.ocd/bin/ocd-restore

That clones my git repo and copies my entire environment into my home directory.

I also have another helper script to keep track of common packages I use across all my systems. It works like this:

    sudo apt-cache update
    sudo apt-get install `~/bin/ocd-missing-debs`

Those simple steps eliminate 95% of the fiddling I used to do when moving into a freshly installed system. The only remaining tweaks deal with differences between distributions or domain-specific configurations, and I write my dotfiles in such a way to accommodate those scenarios. For example, my .bashrc only contains things I'm reasonable sure are portable across all of the systems I use. For host- or domain-specific things, I do the following at the end of my common .bashrc:

    . $HOME/.bashrc_$(hostname -f)
    . $HOME/.bashrc_$(dnsdomainname)

That way, settings are only applied in their appropriate context.

## Managing changes

If I change something on any of my systems, I can easily push the change back to my master git repository. For example:

    $ echo "# Just testing OCD." >> ~/.bashrc
    $ ocd-backup
    ..................... done!

    git status in /home/ksw/.ocd:

    # On branch master
    # Changes not staged for commit:
    # (use "git add <file>..." to update what will be committed)
    # (use "git checkout -- <file>..." to discard changes in working directory)
    #
    # modified: .bashrc
    #
    no changes added to commit (use "git add" and/or "git commit -a")

    git diff in /home/ksw/.ocd:

    diff --git a/.bashrc b/.bashrc
    index 4e127f4..11d24ff 100644
    --- a/.bashrc
    +++ b/.bashrc
    @@ -57,3 +57,4 @@ for file in $SOURCE_FILES;do test -f $file && . $file;done
    # test -f ~/bin/ocd-status && ~/bin/ocd-status
    # touch ~/.bashrc
    #fi

    +# Just testing OCD.

    Commit and push now? (yes/no): yes
    [Editor launches so you may describe the change here.]

    ".git/COMMIT_EDITMSG" 10L, 270C [w]
    [master da7e536] Just testing.
    1 file changed, 1 insertion(+)
    Counting objects: 5, done.
    Delta compression using up to 12 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (3/3), 308 bytes | 0 bytes/s, done.
    Total 3 (delta 2), reused 0 (delta 0)
    To git@github.com:obeyeater/ocd.git
    3599b0b..da7e536 master -> master

## Steal this technique

If you want to use my configuration as a starting point, you can just
branch my git repo and make your own modifications following the workflow
described above.
