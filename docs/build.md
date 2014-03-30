Building an emoncms deb
==============

This guide describes the steps required to build an emoncms .deb file which can then be pushed to the APT repository, or just installed on a local machine.

### Check out repositories

You need to clone both Dave-McCraw/pkg-emoncms and emoncms/emoncms repositories (or equivalent forks).

Export the source you want to package from the emoncms repository with the following command:

    git archive export | gzip > ../emoncms.tar.gz

Do some prep work for the import inside the pkg-emoncms repository:

    git checkout upstream
    git checkout pristine-tar

Now import to the pkg-emoncms repository with the following command:

    git import-orig --pristine-tar --filter=readme.md ../emoncms.tar.gz

(We want to filter out the GitHub readme.md file to prevent conflicts between the two repos.)

Now you need to increment the debian changelogs, specifying your new versions, e.g.:

    dch -v 8.0.8
    dch -v 8.0.8 --news

Don't forget to roll back the Quilt patches before committing anything to Git:

    quilt pop -a
Now commit your changes.

Now build the deb:

    git-buildpackage
You can now install the deb directly, or push it up to the APT repo.

If you want to preserve this for posterity, you need to push a tag (debian/8.0.8, upstream, pristine-tar and master branches back to GitHub).
