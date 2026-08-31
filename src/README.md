# PHASE optics simulation package

## repositories
(UF always pushs to two repositories github is the master, gitea
at PSI as the backup)

git remote -v

origin     git@github.com:flechsig/phase.git
psiorigin  git@gitea.psi.ch:optics/phase.git

building with cmake
===================
cd build
cmake -B . -S ../phasesrv -D option=ON
