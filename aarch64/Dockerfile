FROM scratch
ADD rootfs.tar.gz /

MAINTAINER Richard Clarke <rsclrk@pm.me>
LABEL Description="Fedora 28 base image for Packet.net" Vendor="Packet.net" Version="2018.07.17.00"

ADD qemu-aarch64-static /usr/bin/qemu-aarch64-static

RUN dnf update  ${DNF_OPTS} -y && dnf install ${DNF_OPTS} -y \
	device-mapper-multipath \
	ethstatus \
	hwdata \
	ioping \
	iotop \
	iperf \
	iscsi-initiator-utils \
	keyutils \
	mlocate \
	logrotate \
	make \
	mdadm \
	mg \
	microcode_ctl \
	mtr \
	NetworkManager-team \
	NetworkManager-tui \
	nmap-ncat \
	ntp \
	ntpdate \
	pciutils \
	redhat-lsb-core \
	rsyslog \
	screen \
	socat \
	sysstat \
	tcpdump \
	teamd \
	tmux \
	traceroute \
	tuned \
	vim \
	wget \
	&& dnf clean all

# Remove default eth0 dhcp config
RUN rm -f /etc/sysconfig/network-scripts/ifcfg-eth0

# Reinstall iputils due to non-priv user bug, fix cap
RUN dnf -y reinstall iputils

# Add service to fix POSIX 1003.1e capabilities on ping
RUN bash -c "$(/bin/echo -e "cat > /usr/lib/systemd/system/setcap.service <<EOM\
\n[Unit]\
\nDescription=Setup setcap ping\
\nAfter=multi-user.target\
\n \
\n[Service]\
\nType=oneshot\
\nExecStart=/usr/sbin/setcap 'cap_net_admin,cap_net_raw+ep' /usr/bin/ping\
\nRemainAfterExit=true\
\nStandardOutput=journal\
\n \
\n[Install]\
\nWantedBy=multi-user.target\
\nEOM\n")"
RUN ln -s /usr/lib/systemd/system/setcap.service /etc/systemd/system/setcap.service
RUN ln -s /usr/lib/systemd/system/setcap.service /etc/systemd/system/multi-user.target.wants/setcap.service

# Adjust root account
RUN passwd -d root && passwd -l root

RUN rm /usr/bin/qemu-aarch64-static

# vim: set tabstop=4 shiftwidth=4:
