# Eclipse mirroring

Eclipse has a plugin module that will mirror Eclipse software repositories. So, you like need Eclipse installed to mirror anything.

Eclipse p2 software repositories usually have directory browsing blocked at the root level. Getting a listing of the files can be difficult.

I haven't tested cross version downloading, but it looks like Eclipse directly uses the web site url given on command line and does not use configured repos.

## Mirror Repo

Download Eclipse install archive. (Without using the Oomph or whatever it's called downloader.) I usually get the normal java one.

`eclipse-java-2020-03-R-linux-gtk-x86_64.tar.gz`

Run mirror script.

Eclipse prints continuous errors about log4j being configured incorrectly. It doesn't appear to affect anything.
