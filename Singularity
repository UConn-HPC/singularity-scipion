#-*- mode: rpm-spec; fill-column: 79 -*-
Bootstrap: debootstrap
OSVersion: xenial
MirrorURL:  http://us.archive.ubuntu.com/ubuntu/

%post
# openmpi-dev needs the xenial/universe repo.
cat <<EOF > /etc/apt/sources.list.d/scipion.list
deb http://us.archive.ubuntu.com/ubuntu/ xenial universe
EOF
# Install OS packages.  Skip time consuming apt-get network commands if all
# packages are present.
deps="wget python gcc g++ gfortran openjdk-8-jdk cmake"
deps="$deps libxft-dev libssl-dev libxext-dev libxml2-dev libreadline6"
deps="$deps libquadmath0 libxslt1-dev libxss-dev libgsl0-dev"
deps="$deps libx11-dev libfreetype6-dev"
deps="$deps libopenmpi-dev openmpi-bin "
echo $deps | tr " " "\n" | sort > .deps_needed
dpkg-query -f '${binary:Package}\n' -W | sed 's#:.*##' | sort > .deps_installed
missing=$(join -a 1 -v 1 .deps_needed .deps_installed)
rm -f .deps_needed .deps_installed
[ -z "$missing" ] || { apt-get update && apt-get -y install $missing && apt-get -y upgrade ; }

# Install scipion.
cd /tmp
prefix=/usr
target=$prefix/bin/scipion
url=https://github.com/I2PC/scipion/releases/download/v1.2/scipion_v1.2_2018-04-02_source.tgz.Sources.tgz
tardir=scipion
[ -f "$target" ] || [ -d "$tardir" ] || wget $url -O - | tar -xz
cd scipion
./scipion config <<EOF

EOF
./scipion install -j `nproc`
