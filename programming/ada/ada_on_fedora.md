# ADA on Fedora

## Packages needed

```bash
dnf install -y fedora-gnat-project-common gprbuild
# Did this pull in gcc-gnat automatically?
```

Example compile

```bash
gnatmake -g hello_world.adb
gprbuild -p hello_world.adb
gprclean -p hello_world.adb
```
