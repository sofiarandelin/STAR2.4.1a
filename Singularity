Bootstrap: docker
From: centos:8
Stage: build

%setup

%files

%help

   Bionformatics tools.

   Complete list of installed tools:
      - STAR 2.4.1a

## END HELP



%post

   NOW=`date`
   echo "export NOW=\"${NOW}\"" >> $SINGULARITY_ENVIRONMENT
   set -e

   dnf -y makecache
   dnf -y group install "Development Tools"
   dnf -y install --allowerasing  hostname which dnf-utils \
     python3-pip git zlib zlib-devel bzip2 bzip2-devel \
     xz xz-devel libcurl libcurl-devel ncurses ncurses-devel \
     unzip wget gnuplot rsync java-1.8.0-openjdk java-1.8.0-openjdk-devel \
     openssl-devel libffi-devel python36-devel

   ln -s /usr/bin/python3 /usr/bin/python
   ln -s /usr/bin/pip3 /usr/bin/pip

   pip install numpy pandas matplotlib scikit-learn scipy opencv-contrib-python

   # Intel Threading Building Blocks 2019 U8
   echo "/usr/local/lib" >> /etc/ld.so.conf.d/usr_local_lib.conf
   rm -rf tbb
   git clone https://github.com/intel/tbb
   cd tbb
   make -j 8
   find . -name '*so' | grep release | xargs -I{} cp {} /usr/local/lib/  # Copy release libs to sys dir
   find . -name '*so.2' | grep release | xargs -I{} cp {} /usr/local/lib/
   cp -R include/* /usr/local/include/
   cd ..
   ldconfig
   mkdir -p /usr/local/lib64/python3.6/site-packages


%runscript

    echo "Container was created $NOW"
    echo "Arguments received: $*"
    exec echo "$@"


%labels
   Author "tomi.hakkinen@tuni.fi and francesco.tabaro@tuni.fi"
   Version 0.29

%environment
#    GLOBAL=variables
#    AVAILABLE="to all apps"
   export PATH=$PATH:/usr/bin:/usr/local/bin/homer/bin:/opt/ucsc_tools:/opt/Bismark-0.22.3
   LANG="en_US.UTF-8"
   export LANG
   PYTHONWARNINGS=ignore::yaml.YAMLLoadWarning

# ######################### STAR ###################################

%apprun star
   exec star

%appinstall star
   rm -rf STAR
   git clone https://github.com/alexdobin/STAR.git
   cd STAR
   git checkout 1fd81948069bd1c5635a2e1e283d6b7183816dae
   cd source
   make -j 8 STAR
   cp STAR /usr/local/bin/
   ln -sf /usr/local/bin/STAR /usr/local/bin/star
   cd ..
   cd ..

%apphelp star
   STAR 2.4.1a. https://github.com/alexdobin/STAR
