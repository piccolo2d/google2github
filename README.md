If nobody steps up and raises objections with the migration results:

On August 17th I (https://github.com/mro) will

- Redirect from Google Code to http://piccolo2d.mro.name/community.html via https://code.google.com/p/piccolo2d/adminAdvanced (found at https://code.google.com/p/support-tools/wiki/MigratingToGitHub#URL_Redirection)

## Code Migration

    $ repo=piccolo2d.java,piccolo2d.net,pocketpiccolo2d.net,site
    $ git svn clone http://piccolo2d.googlecode.com/svn/$repo/ --no-metadata --stdlayout --authors-file=$HOME/Downloads/p2d.authors.txt $repo
    $ cd $repo/
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
    $ git remote add github git@github.com:piccolo2d/$repo.git
    $ git push --mirror --force github

## clean names

    git filter-branch -f --commit-filter '
      if [ "$GIT_AUTHOR_EMAIL" = "github@mro.name" ];
      then
        GIT_AUTHOR_NAME="Marcus Rohrmoser";
        GIT_AUTHOR_EMAIL="mrohrmoser@acm.org";
        GIT_COMMITTER_NAME="$GIT_AUTHOR_NAME";
        GIT_COMMITTER_EMAIL="$GIT_AUTHOR_EMAIL";
        git commit-tree "$@";
      else
        git commit-tree "$@";
      fi' HEAD
