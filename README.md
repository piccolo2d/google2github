If nobody steps up and helps with the migration:

On August 17th I (https://github.com/mro) will

- make http://piccolo2d.mro.name a CNAME of http://piccolo2d.github.io (see https://help.github.com/articles/about-custom-domains-for-github-pages-sites/)
- edit http://piccolo2d.mro.name/community.html to match the new situation
- Redirect to http://piccolo2d.mro.name/community.html via https://code.google.com/p/piccolo2d/adminAdvanced (found at https://code.google.com/p/support-tools/wiki/MigratingToGitHub#URL_Redirection)

## piccolo2d.java migration

    $ git svn clone http://piccolo2d.googlecode.com/svn/piccolo2d.java/ --no-metadata --stdlayout --authors-file=$HOME/Downloads/p2d.authors.txt piccolo2d.java
    $ cd piccolo2d.java/
    $ git remote add github git@github.com:mro/piccolo2d.java.git
    # http://stackoverflow.com/a/14800155
    $ git for-each-ref --format="%(refname:short) %(objectname)" refs/remotes/tags \
    | while read BRANCH REF
      do
            TAG_NAME=${BRANCH#*/}
            BODY="$(git log -1 --format=format:%B $REF)"

            echo "ref=$REF parent=$(git rev-parse $REF^) tagname=$TAG_NAME body=$BODY" >&2

            git tag -a -m "$BODY" $TAG_NAME $REF^  &&\
            git branch -r -d $BRANCH
      done
    $ for b in $(git branch -r) ; do git checkout "$b" ; git checkout -b "$b" ; done 
    $ git push --mirror --force github
