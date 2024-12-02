# Coverity with fortran.io

Testing Coverty's anaysis with fortran-machine from http://fortran.io

fortran-machine setup is for Ubuntu. It compiles on Centos 7 but does not link.

In Coverity Connect made a project "fortran.io" with stream "fortran-machine-b1".

```bash
sudo /opt/coverity/cov-analysis-linux64-2019.12/bin/cov-configure --gcc
cov-build -dir idir make clean fortran_fcgi
```

That doesn't work because fortran doesn't use cov-build. It is done by a non-compiling program cov-run-fortran.

```bash
cov-run-fortran --dir idir --configuration gfortran -f90 -- fortran_fcgi.f90
find . -iname "*.f90" -exec cov-run-fortran --dir idir  --configuration gfortran -f90 -- {}\+

# no cov-analyze -dir idir
cov-commit-defects --dir idir --stream "fortran-machine-b1" --host lt-statics.viprcenter.com --user wymanco
```

```bash
find . -iname "*.f90" -exec cov-run-fortran --dir idir  --configuration gfortran -f90 -ancmpl -anprg -anref -rigorous -- {}\+
```
