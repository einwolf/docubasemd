# Coverity network ports

2/22/2020

Network ports discovered that Coverity Connect uses.

|Protocol     |Port |Comment
|---          |---  |---
|tcp,http,ssl |8443 |Web page
|tcp,http     |8080 |Seen in `cov-run-desktop --setup`
|tcp          |9090 |Seen in cov-commit-defects
