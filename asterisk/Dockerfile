FROM centos:centos7
MAINTAINER MrDigger <mrdigger@mail.ru>

#===============================================================================
# Install Asterisk's dependencies:
#===============================================================================

#-------------------------------------------------------------------------------
# All essential (for v.15): 
#-------------------------------------------------------------------------------

WORKDIR /etc/yum.repos.d

RUN yum -y install gcc gcc-c++ make \
                   wget tar gzip bzip2 \
                   kernel-headers kernel-devel \
                   libuuid-devel \
                   sqlite-devel  \
                   ncurses-devel \
                   libtermcap-devel \
                   libedit-devel \
                   net-snmp-devel \
                   openssl-devel \
                   sendmail \
                   sendmail-cf \
                   caching-nameserver \
                   newt-devel \
                   libxml2-devel \ 
                   libtiff-devel \
                   audiofile-devel \
                   gtk2-devel \
                   libtool \
                   uuid-devel \
                   gettext\
                   sox \      
                   xinetd \         
                   net-tools \

#-------------------------------------------------------------------------------
# Install latest libjansson (mandatory): [WORKDIR /etc/yum.repos.d]
#-------------------------------------------------------------------------------

 && wget https://download.opensuse.org/repositories/home:nxtg_octopus:CentOS:asterisk-15/CentOS_7/home:nxtg_octopus:CentOS:asterisk-15.repo \
 && yum -y install libjansson libjansson-devel \

 && yum clean all

#===============================================================================
# Download the Asterisk sources:
#===============================================================================

#-------------------------------------------------------------------------------
# Asterisk latest version:
#-------------------------------------------------------------------------------

#--- The possible ways:
# WORKDIR /usr/src
# RUN yum -y install git && git clone http://gerrit.asterisk.org/asterisk asterisk
#--- or:
# RUN yum -y install git && git clone -b 15 http://gerrit.asterisk.org/asterisk asterisk

WORKDIR /usr/src
RUN release_file=asterisk-15-current.tar.gz \
 && wget https://downloads.asterisk.org/pub/telephony/asterisk/$release_file \
 && tar -zxf $release_file \
 && rm  -rf  $release_file \
 && mv `ls -1 | grep asterisk-` asterisk


#-------------------------------------------------------------------------------
# Add MP3 support:
#-------------------------------------------------------------------------------

WORKDIR /usr/src/asterisk
RUN yum -y install svn \
 && ./contrib/scripts/get_mp3_source.sh 

#===============================================================================
# Install options:
#===============================================================================

#-------------------------------------------------------------------------------
# Install the "Lame" (wav -> mp3 converter):
#-------------------------------------------------------------------------------

RUN rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro  \
 && rpm -Uvh     http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-1.el7.nux.noarch.rpm \
 && yum -y install lame

#-------------------------------------------------------------------------------
# Install mpg123 (to play MOH with mp3 files):
#-------------------------------------------------------------------------------

WORKDIR /etc/yum.repos.d
RUN wget http://downloads2.goautodial.org/centos/7/goautodial.repo \
 && yum -y update \
 && yum -y install libmpg123-devel.x86_64 mpg123

#===============================================================================
# Build the Asterisk 
#===============================================================================

#-------------------------------------------------------------------------------
# Configure:
#-------------------------------------------------------------------------------

WORKDIR /usr/src/asterisk

RUN ./configure --libdir=/usr/lib64 --without-pjproject-bundled \

#-------------------------------------------------------------------------------
# Menu select options:
#-------------------------------------------------------------------------------

 && make menuselect.makeopts \
 && menuselect/menuselect \
  --disable BUILD_NATIVE \
  --enable chan_sip \  
  --enable chan_ooh323 \ 
  --enable cdr_csv \
  --enable cdr_custom \
  --enable format_mp3 \
  --enable res_snmp \
  --enable res_http_websocket \
  --enable res_hep_rtcp \

#-------------------------------------------------------------------------------
# Insatall:
#-------------------------------------------------------------------------------

 && make \ 
 && make install \
 && make samples 

#===============================================================================
# Start:
#===============================================================================

WORKDIR /

CMD asterisk -f
