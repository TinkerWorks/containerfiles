FROM ghcr.io/linuxserver/transmission

# Set transmission i/o priority as idle
RUN sed -i 's:/usr/bin/transmission-daemon:/bin/ionice -c3 /usr/bin/transmission-daemon:g' /etc/s6-overlay/s6-rc.d/svc-transmission/run
