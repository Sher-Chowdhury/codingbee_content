---
ID: 313
post_title: 'Git &#8211; The Git command line'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/git/git-the-git-command-line
published: true
post_date: 2015-03-07 00:00:00
---
The best way to use git is from the command line. There are also a bunch of gui git clients available. These gui clients are essentially a wrapper around the git command line. However none of the gui clients fully utilise the git command line. Hence it is best to invest the time in learning how to use the git command line so that you have full control over git. 

On windows, you can use git from the powershell terminal. However the <a href="http://git-scm.com/downloads">git installation</a> also comes with it's own git terminal called "git-bash" which enhance your experience in using git (e.g. syntax highlighting). 


Here's git's main help info:


<pre>
[vagrant@localhost ~]$ git help
usage: git [--version] [--exec-path[=GIT_EXEC_PATH]] [--html-path]
           [-p|--paginate|--no-pager] [--no-replace-objects]
           [--bare] [--git-dir=GIT_DIR] [--work-tree=GIT_WORK_TREE]
           [--help] COMMAND [ARGS]

The most commonly used git commands are:
   add        Add file contents to the index
   bisect     Find by binary search the change that introduced a bug
   branch     List, create, or delete branches
   checkout   Checkout a branch or paths to the working tree
   clone      Clone a repository into a new directory
   commit     Record changes to the repository
   diff       Show changes between commits, commit and working tree, etc
   fetch      Download objects and refs from another repository
   grep       Print lines matching a pattern
   init       Create an empty git repository or reinitialize an existing one
   log        Show commit logs
   merge      Join two or more development histories together
   mv         Move or rename a file, a directory, or a symlink
   pull       Fetch from and merge with another repository or a local branch
   push       Update remote refs along with associated objects
   rebase     Forward-port local commits to the updated upstream head
   reset      Reset current HEAD to the specified state
   rm         Remove files from the working tree and from the index
   show       Show various types of objects
   status     Show the working tree status
   tag        Create, list, delete or verify a tag object signed with GPG

See 'git help COMMAND' for more information on a specific command.
[vagrant@localhost ~]$

</pre>

However this doesn't list all the commands, just the most commonly used git commands. If you do "git help help", then you'll discover there is an "--all" option, which if you try, you'll discover a lot more git commands:

<pre>
[vagrant@localhost ~]$ git help --all
usage: git [--version] [--exec-path[=GIT_EXEC_PATH]] [--html-path]
           [-p|--paginate|--no-pager] [--no-replace-objects]
           [--bare] [--git-dir=GIT_DIR] [--work-tree=GIT_WORK_TREE]
           [--help] COMMAND [ARGS]

available git commands in '/usr/libexec/git-core'
-------------------------------------------------
  add                 filter-branch       merge-subtree       rev-list
  add--interactive    fmt-merge-msg       merge-tree          rev-parse
  am                  for-each-ref        mergetool           revert
  annotate            format-patch        mktag               rm
  apply               fsck                mktree              send-pack
  archive             fsck-objects        mv                  shell
  bisect              gc                  name-rev            shortlog
  bisect--helper      get-tar-commit-id   notes               show
  blame               grep                pack-objects        show-branch
  branch              hash-object         pack-redundant      show-index
  bundle              help                pack-refs           show-ref
  cat-file            http-backend        patch-id            stage
  check-attr          http-fetch          peek-remote         stash
  check-ref-format    http-push           prune               status
  checkout            imap-send           prune-packed        stripspace
  checkout-index      index-pack          pull                submodule
  cherry              init                push                symbolic-ref
  cherry-pick         init-db             quiltimport         tag
  clean               instaweb            read-tree           tar-tree
  clone               log                 rebase              unpack-file
  commit              lost-found          rebase--interactive unpack-objects
  commit-tree         ls-files            receive-pack        update-index
  config              ls-remote           reflog              update-ref
  count-objects       ls-tree             relink              update-server-info
  describe            mailinfo            remote              upload-archive
  diff                mailsplit           remote-ftp          upload-pack
  diff-files          merge               remote-ftps         var
  diff-index          merge-base          remote-http         verify-pack
  diff-tree           merge-file          remote-https        verify-tag
  difftool            merge-index         repack              web--browse
  difftool--helper    merge-octopus       replace             whatchanged
  fast-export         merge-one-file      repo-config         write-tree
  fast-import         merge-ours          request-pull
  fetch               merge-recursive     rerere
  fetch-pack          merge-resolve       reset

See 'git help COMMAND' for more information on a specific command.
[vagrant@localhost ~]$

</pre>