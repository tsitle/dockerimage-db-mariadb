#
# Based on
#   https://github.com/linuxserver/docker-mariadb
# and
#   https://github.com/linuxserver/docker-baseimage-ubuntu branch bionic
#

ARG CF_SRC_OS_IMAGE

FROM ${CF_SRC_OS_IMAGE}

ARG CF_UBUNTU_RELEASE
ARG CF_CPUARCH_DEB_DIST
ENV CF_CPUARCH_DEB_DIST=${CF_CPUARCH_DEB_DIST}

ARG CF_MARIADB_VERSION

# ------------------------------------------------------------------------------

RUN \
	apt-get update \
	&& apt-get upgrade -y \
	&& apt-get dist-upgrade -y

# ------------------------------------------------------------------------------
# ------------------------------------------------------------------------------
# beg lsio-baseimage_ubuntu_bionic (v18.04.2)
# ------------------------------------------------------------------------------
# ------------------------------------------------------------------------------
ARG CF_S6_OVERLAY_VERSION
ARG CF_CPUARCH_S6_OVERLAY
ENV CF_CPUARCH_S6_OVERLAY=${CF_CPUARCH_S6_OVERLAY}

#ARG CF_CPUARCH_QEMU
#ENV CF_CPUARCH_QEMU=${CF_CPUARCH_QEMU}

ARG ARCH=${CF_CPUARCH_DEB_DIST}
ENV ARCH=${ARCH}

# set version for s6 overlay
ARG OVERLAY_VERSION=${CF_S6_OVERLAY_VERSION}
ARG OVERLAY_ARCH=${CF_CPUARCH_S6_OVERLAY}

# set environment variables
ENV DEBIAN_FRONTEND="noninteractive"
ENV \
	HOME="/root" \
	LANGUAGE="en_US.UTF-8" \
	LANG="en_US.UTF-8" \
	TERM="xterm"

# copy S6 overlay tarball
COPY cache/s6_overlay/s6-overlay-${OVERLAY_VERSION}-${OVERLAY_ARCH}.tgz /tmp/

# copy QEMU
#COPY cache/qemu_binary_static/qemu-${CF_CPUARCH_QEMU}-static /usr/bin/

# copy sources list
#COPY files/lsio-baseimage_ubuntu_${CF_UBUNTU_RELEASE}/sources.list.${CF_CPUARCH_DEB_DIST} /etc/apt/

RUN \
	#echo "**** install apt-utils and locales ****"; \
	#apt-get update \
	#&& apt-get upgrade -y \
	#&& apt-get dist-upgrade -y \
	#&& apt-get install -y --no-install-recommends \
	#		apt-utils \
	#		locales \
	#|| exit 1; \
	#
	#echo "**** install packages ****"; \
	#apt-get install -y --no-install-recommends \
	#		curl \
	#		tzdata \
	#|| exit 1; \
	#
	#echo "**** generate locale ****"; \
	#locale-gen en_US.UTF-8 \
	#|| exit 1; \
	#
	echo "**** add s6 overlay ****"; \
	# ------------------------------------------------
	#curl -o \
	#		/tmp/s6-overlay.tar.gz -L \
	#		"https://github.com/just-containers/s6-overlay/releases/download/${OVERLAY_VERSION}/s6-overlay-${OVERLAY_ARCH}.tar.gz" \
	#&& tar xfz \
	#		/tmp/s6-overlay.tar.gz -C / \
	tar xzf \
			/tmp/s6-overlay-${OVERLAY_VERSION}-${OVERLAY_ARCH}.tgz -C / \
	|| exit 1; \
	# ------------------------------------------------
	#
	echo "**** create abc user and make our folders ****"; \
	useradd -u 911 -U -d /config -s /bin/false abc \
	&& usermod -G users abc \
	&& mkdir -p \
			/app \
			/config \
			/defaults \
	|| exit 1; \
	#if [ "$ARCH" != "amd64" ]; then \
	#	#echo "**** add qemu ****"; \
	#	# ------------------------------------------------
	#	#curl -o \
	#	#		/usr/bin/qemu-${CF_CPUARCH_QEMU}-static -L \
	#	#		"https://lsio-ci.ams3.digitaloceanspaces.com/qemu-${CF_CPUARCH_QEMU}-static" \
	#	#chmod +x /usr/bin/qemu-${CF_CPUARCH_QEMU}-static \
	#	# ------------------------------------------------
	#else \
	#	echo -n; \
	#fi; \
	#
	echo "**** cleanup ****"; \
	apt-get clean \
	&& rm -rf \
			/tmp/* \
			/var/lib/apt/lists/* \
			/var/tmp/*

# add local files
COPY files/lsio-baseimage_ubuntu_${CF_UBUNTU_RELEASE}/fs_root/ /

#ENTRYPOINT ["/init"]
# ------------------------------------------------------------------------------
# ------------------------------------------------------------------------------
# end lsio-baseimage_ubuntu_bionic
# ------------------------------------------------------------------------------
# ------------------------------------------------------------------------------

# ------------------------------------------------------------------------------
# ------------------------------------------------------------------------------
# beg lsio-mariadb (v10.1)
# ------------------------------------------------------------------------------
# ------------------------------------------------------------------------------
# environment variables
ENV DEBIAN_FRONTEND="noninteractive"
ENV MYSQL_DIR="/config"
#ENV DATADIR=$MYSQL_DIR/databases
ENV DATADIR=/var/lib/mysql

#RUN \
#	apt-get update \
#	&& apt-get install -y --no-install-recommends \
#			gnupg

RUN \
	apt-get update; \
	#echo "add mariadb repository ****"; \
	#echo "(redundant on armhf platform, but added for consistent dockerfile on all platforms) ****"; \
	#apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8 \
	#&& add-apt-repository 'deb [arch=amd64,arm64,ppc64el] http://mirror.i3d.net/pub/mariadb/repo/10.1/ubuntu bionic main' \
	#|| exit 1; \
	#
	echo "**** install runtime packages ****"; \
	MARIADB_PACK_VER="$(apt-cache policy mariadb-server | grep "Candidate: " | grep "${CF_MARIADB_VERSION}\." | cut -f2- -d: | tr -d [:space:])"; \
	echo "MARIADB_PACK_VER='$MARIADB_PACK_VER'"; \
	[ -n "$MARIADB_PACK_VER" ] \
	#&& apt-cache show mariadb-server \
	&& apt-get install -y --no-install-recommends \
			mariadb-server=${MARIADB_PACK_VER} \
	|| exit 1; \
	#
	echo "**** cleanup ****"; \
	rm -rf \
			/tmp/* \
			/var/lib/apt/lists/* \
			/var/lib/mysql \
			/var/tmp/* \
	&& mkdir -p \
			/var/lib/mysql

# add local files
COPY files/lsio-mariadb/fs_root/ /

EXPOSE 3306

#VOLUME /config
VOLUME /var/lib/mysql
# ------------------------------------------------------------------------------
# ------------------------------------------------------------------------------
# end lsio-mariadb (v10.1)
# ------------------------------------------------------------------------------
# ------------------------------------------------------------------------------

ENV DEBIAN_FRONTEND="noninteractive"

# startup script
COPY files/custom/docker-entrypoint.sh /usr/local/bin/

# healthcheck script
COPY files/custom/healthcheck.sh /usr/local/bin/

# other scripts
COPY \
	files/custom/update_db_root_pw.sh \
	files/custom/dbmysql-create_db_and_user.sh \
	files/custom/dbmysql-dump_or_import.sh \
	files/custom/inc-dbmysql.sh \
	/root/

RUN \
	mkdir /root/extDbFiles \
	&& chmod 700 \
			/usr/local/bin/docker-entrypoint.sh \
			/usr/local/bin/healthcheck.sh \
			/root/update_db_root_pw.sh \
			/root/dbmysql-create_db_and_user.sh \
			/root/dbmysql-dump_or_import.sh \
			/root/inc-dbmysql.sh \
	&& chown root:root \
			/usr/local/bin/docker-entrypoint.sh \
			/usr/local/bin/healthcheck.sh \
			/root/update_db_root_pw.sh \
			/root/dbmysql-create_db_and_user.sh \
			/root/dbmysql-dump_or_import.sh \
			/root/inc-dbmysql.sh

#
ENV DEBIAN_FRONTEND=dialog

WORKDIR /root/

VOLUME /root/extDbFiles

ENTRYPOINT ["docker-entrypoint.sh"]
