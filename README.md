# GitHub self-hosted runner Dockerfile and Kubernetes configuration

This repository contains a Dockerfile that builds a Docker image suitable for running a self-hosted GitHub runner. A Kubernetes Deployment file is also included that you can use as an example on how to deploy this container to a Kubernetes cluster.

## Building the container

```txt
Ajays-MBP:github-action-runners ajay$ docker build -t github-action-runners .
[+] Building 230.4s (11/11) FINISHED                                                                                                                                        
 => [internal] load .dockerignore                                                                                                                                      0.0s
 => => transferring context: 2B                                                                                                                                        0.0s
 => [internal] load build definition from Dockerfile                                                                                                                   0.0s
 => => transferring dockerfile: 37B                                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/debian:buster-slim                                                                                                  1.9s
 => [internal] load build context                                                                                                                                      0.0s
 => => transferring context: 587B                                                                                                                                      0.0s
 => [1/6] FROM docker.io/library/debian:buster-slim@sha256:4d208d79338beaa197912496d0791921a31d9c58fa6fbd299787fab37cc893dd                                           21.6s
 => => resolve docker.io/library/debian:buster-slim@sha256:4d208d79338beaa197912496d0791921a31d9c58fa6fbd299787fab37cc893dd                                            0.0s
 => => sha256:4d208d79338beaa197912496d0791921a31d9c58fa6fbd299787fab37cc893dd 984B / 984B                                                                             0.0s
 => => sha256:603e7e0b2c5ee5dcfd1b1ba07b4598dd481b6afa6dcf348399428e72b0370a35 529B / 529B                                                                             0.0s
 => => sha256:92a9b50a62b45ede9878156909ea76de9d04716aa0cdfe175bd2d28e34b87149 1.46kB / 1.46kB                                                                         0.0s
 => => sha256:9fbefa3370776b7ec7633cf07efc14cc24e0c0cd53893ad0e7e3f44ffdc1bedb 27.14MB / 27.14MB                                                                       6.1s
 => => extracting sha256:9fbefa3370776b7ec7633cf07efc14cc24e0c0cd53893ad0e7e3f44ffdc1bedb                                                                             13.8s
 => [2/6] RUN apt-get update     && apt-get install -y         curl         sudo         git         jq         iputils-ping     && apt-get clean     && rm -rf /var  49.4s
 => [3/6] WORKDIR /home/github                                                                                                                                         0.2s 
 => [4/6] RUN GITHUB_RUNNER_VERSION=$(curl --silent "https://api.github.com/repos/actions/runner/releases/latest" | jq -r '.tag_name[1:]')     && curl -Ls https://  111.5s 
 => [5/6] COPY --chown=github:github entrypoint.sh runsvc.sh ./                                                                                                        0.2s 
 => [6/6] RUN sudo chmod u+x ./entrypoint.sh ./runsvc.sh                                                                                                               1.1s
 => exporting to image                                                                                                                                                44.3s
 => => exporting layers                                                                                                                                               44.3s
 => => writing image sha256:5bbba2c3a709fab922c10f395c8f173eb711cbe7b7389a45f09a8112463541eb                                                                           0.0s
 => => naming to docker.io/library/github-action-runners
```

## Features

* Repository runners
* Organizational runners
* Labels
* Graceful shutdown
* Support for installing additional debian packages
* Auto-update after the release of a new version

## Examples

Register a runner to a repository.

```txt
Ajays-MBP:github-action-runners ajay$ docker run --name github-action-runners \
>      -e GITHUB_OWNER=ajaychinthapalli \
>      -e GITHUB_REPOSITORY=github-action-runners \
>      -e GITHUB_PAT=<PAT> \
>      github-action-runners
Requesting token at 'https://api.github.com/repos/ajaychinthapalli/github-action-runners/actions/runners/registration-token'

--------------------------------------------------------------------------------
|        ____ _ _   _   _       _          _        _   _                      |
|       / ___(_) |_| | | |_   _| |__      / \   ___| |_(_) ___  _ __  ___      |
|      | |  _| | __| |_| | | | | '_ \    / _ \ / __| __| |/ _ \| '_ \/ __|     |
|      | |_| | | |_|  _  | |_| | |_) |  / ___ \ (__| |_| | (_) | | | \__ \     |
|       \____|_|\__|_| |_|\__,_|_.__/  /_/   \_\___|\__|_|\___/|_| |_|___/     |
|                                                                              |
|                       Self-hosted runner registration                        |
|                                                                              |
--------------------------------------------------------------------------------

# Authentication


√ Connected to GitHub

# Runner Registration




√ Runner successfully added
√ Runner connection is good

# Runner settings


√ Settings Saved.

.path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
Starting Runner listener with startup type: service
Started listener process, pid: 80
Started running service

√ Connected to GitHub

Current runner version: '2.303.0'
2023-04-19 03:35:33Z: Listening for Jobs

```

Set labels on the runner.

```txt
Ajays-MBP:github-action-runners ajay$ docker run --name github-action-runners \
>     -e GITHUB_OWNER=ajaychinthapalli \
>     -e GITHUB_REPOSITORY=github-action-runners \
>     -e GITHUB_PAT=<PAT> \
>     -e RUNNER_LABELS=k8s-runner-s \
>     github-action-runners
Requesting token at 'https://api.github.com/repos/ajaychinthapalli/github-action-runners/actions/runners/registration-token'

--------------------------------------------------------------------------------
|        ____ _ _   _   _       _          _        _   _                      |
|       / ___(_) |_| | | |_   _| |__      / \   ___| |_(_) ___  _ __  ___      |
|      | |  _| | __| |_| | | | | '_ \    / _ \ / __| __| |/ _ \| '_ \/ __|     |
|      | |_| | | |_|  _  | |_| | |_) |  / ___ \ (__| |_| | (_) | | | \__ \     |
|       \____|_|\__|_| |_|\__,_|_.__/  /_/   \_\___|\__|_|\___/|_| |_|___/     |
|                                                                              |
|                       Self-hosted runner registration                        |
|                                                                              |
--------------------------------------------------------------------------------

# Authentication


√ Connected to GitHub

# Runner Registration




√ Runner successfully added
√ Runner connection is good

# Runner settings


√ Settings Saved.

.path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
Starting Runner listener with startup type: service
Started listener process, pid: 78
Started running service

√ Connected to GitHub

Current runner version: '2.303.0'
2023-04-19 03:44:01Z: Listening for Jobs

```

Install additional tools on the runner.

```txt
Ajays-MBP:github-action-runners ajay$ docker run --name github-action-runners \
>     -e GITHUB_OWNER=ajaychinthapalli \
>     -e GITHUB_REPOSITORY=github-action-runners \
>     -e GITHUB_PAT=<PAT> \
>     -e ADDITIONAL_PACKAGES=firefox-esr,chromium \
>     github-action-runners
Installing additional packages: firefox-esr chromium
Hit:1 http://deb.debian.org/debian buster InRelease
Hit:2 http://deb.debian.org/debian-security buster/updates InRelease
Hit:3 http://deb.debian.org/debian buster-updates InRelease
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  adwaita-icon-theme at-spi2-core chromium-common chromium-sandbox cpp cpp-8
  dbus dbus-user-session dconf-gsettings-backend dconf-service dmsetup
  fontconfig fontconfig-config fonts-dejavu-core fonts-liberation
  glib-networking glib-networking-common glib-networking-services
  gsettings-desktop-schemas gtk-update-icon-cache hicolor-icon-theme
  i965-va-driver intel-media-va-driver libaacs0 libaom0 libapparmor1
  libargon2-1 libasound2 libasound2-data libasyncns0 libatk-bridge2.0-0
  libatk1.0-0 libatk1.0-data libatomic1 libatspi2.0-0 libauthen-sasl-perl
  libavahi-client3 libavahi-common-data libavahi-common3 libavcodec58
  libavformat58 libavutil56 libbdplus0 libbluray2 libcairo-gobject2 libcairo2
  libchromaprint1 libcodec2-0.8.1 libcolord2 libcroco3 libcryptsetup12
  libcrystalhd3 libcups2 libdata-dump-perl libdatrie1 libdbus-1-3
  libdbus-glib-1-2 libdconf1 libdevmapper1.02.1 libdrm-amdgpu1 libdrm-common
  libdrm-intel1 libdrm-nouveau2 libdrm-radeon1 libdrm2 libelf1
  libencode-locale-perl libepoxy0 libevent-2.1-6 libfile-basedir-perl
  libfile-desktopentry-perl libfile-listing-perl libfile-mimeinfo-perl
  libflac8 libfont-afm-perl libfontconfig1 libfontenc1 libfreetype6
  libfribidi0 libgbm1 libgdk-pixbuf2.0-0 libgdk-pixbuf2.0-bin
  libgdk-pixbuf2.0-common libgl1 libgl1-mesa-dri libglapi-mesa libglib2.0-0
  libglib2.0-data libglvnd0 libglx-mesa0 libglx0 libgme0 libgomp1 libgpm2
  libgraphite2-3 libgsm1 libgtk-3-0 libgtk-3-bin libgtk-3-common
  libgudev-1.0-0 libharfbuzz0b libhtml-form-perl libhtml-format-perl
  libhtml-parser-perl libhtml-tagset-perl libhtml-tree-perl
  libhttp-cookies-perl libhttp-daemon-perl libhttp-date-perl
  libhttp-message-perl libhttp-negotiate-perl libice6 libidn11 libigdgmm5
  libimobiledevice6 libio-html-perl libio-socket-ssl-perl libio-stringy-perl
  libip4tc0 libipc-system-simple-perl libisl19 libjbig0 libjpeg62-turbo
  libjson-c3 libjson-glib-1.0-0 libjson-glib-1.0-common libjsoncpp1 libkmod2
  liblcms2-2 libllvm7 liblwp-mediatypes-perl liblwp-protocol-https-perl
  libmailtools-perl libminizip1 libmp3lame0 libmpc3 libmpfr6 libmpg123-0
  libncurses6 libnet-dbus-perl libnet-http-perl libnet-smtp-ssl-perl
  libnet-ssleay-perl libnspr4 libnss-systemd libnss3 libnuma1 libogg0
  libopenjp2-7 libopenmpt0 libopus0 libpam-systemd libpango-1.0-0
  libpangocairo-1.0-0 libpangoft2-1.0-0 libpciaccess0 libpixman-1-0 libplist3
  libpng16-16 libpolkit-agent-1-0 libpolkit-backend-1-0 libpolkit-gobject-1-0
  libprocps7 libproxy1v5 libpulse0 libre2-5 librest-0.7-0 librsvg2-2
  librsvg2-common libsensors-config libsensors5 libshine3 libsm6 libsnappy1v5
  libsndfile1 libsoup-gnome2.4-1 libsoup2.4-1 libsoxr0 libspeex1 libsqlite3-0
  libssh-gcrypt-4 libswresample3 libtext-iconv-perl libthai-data libthai0
  libtheora0 libtie-ixhash-perl libtiff5 libtimedate-perl libtry-tiny-perl
  libtwolame0 libu2f-udev libupower-glib3 liburi-perl libusb-1.0-0 libusbmuxd4
  libva-drm2 libva-x11-2 libva2 libvdpau-va-gl1 libvdpau1 libvorbis0a
  libvorbisenc2 libvorbisfile3 libvpx5 libwavpack1 libwayland-client0
  libwayland-cursor0 libwayland-egl1 libwayland-server0 libwebp6 libwebpdemux2
  libwebpmux3 libwrap0 libwww-perl libwww-robotrules-perl libx11-protocol-perl
  libx11-xcb1 libx264-155 libx265-165 libxaw7 libxcb-dri2-0 libxcb-dri3-0
  libxcb-glx0 libxcb-present0 libxcb-render0 libxcb-shape0 libxcb-shm0
  libxcb-sync1 libxcb-xfixes0 libxcomposite1 libxcursor1 libxdamage1
  libxfixes3 libxft2 libxi6 libxinerama1 libxkbcommon0 libxml-parser-perl
  libxml-twig-perl libxml-xpathengine-perl libxml2 libxmu6 libxpm4 libxrandr2
  libxrender1 libxshmfence1 libxslt1.1 libxt6 libxtst6 libxv1 libxvidcore4
  libxxf86dga1 libxxf86vm1 libzvbi-common libzvbi0 mesa-va-drivers
  mesa-vdpau-drivers notification-daemon perl-openssl-defaults policykit-1
  procps psmisc sensible-utils shared-mime-info systemd systemd-sysv ucf udev
  upower usbmuxd va-driver-all vdpau-driver-all x11-common x11-utils
  x11-xserver-utils xdg-user-dirs xdg-utils xkb-data
Suggested packages:
  chromium-l10n chromium-shell chromium-driver cpp-doc gcc-8-locales
  fonts-stix | otf-stix fonts-lmodern libcanberra0 pulseaudio
  i965-va-driver-shaders libasound2-plugins alsa-utils libdigest-hmac-perl
  libgssapi-perl libbluray-bdj colord firmware-crystalhd cups-common gpm gvfs
  libusbmuxd-tools liblcms2-utils libcrypt-ssleay-perl opus-tools pciutils
  librsvg2-bin lm-sensors speex libauthen-ntlm-perl libunicode-map8-perl
  libunicode-string-perl xml-twig-tools systemd-container nvidia-vdpau-driver
  nvidia-legacy-340xx-vdpau-driver nvidia-legacy-304xx-vdpau-driver mesa-utils
  nickle cairo-5c xorg-docs-core
The following NEW packages will be installed:
  adwaita-icon-theme at-spi2-core chromium chromium-common chromium-sandbox
  cpp cpp-8 dbus dbus-user-session dconf-gsettings-backend dconf-service
  dmsetup firefox-esr fontconfig fontconfig-config fonts-dejavu-core
  fonts-liberation glib-networking glib-networking-common
  glib-networking-services gsettings-desktop-schemas gtk-update-icon-cache
  hicolor-icon-theme i965-va-driver intel-media-va-driver libaacs0 libaom0
  libapparmor1 libargon2-1 libasound2 libasound2-data libasyncns0
  libatk-bridge2.0-0 libatk1.0-0 libatk1.0-data libatomic1 libatspi2.0-0
  libauthen-sasl-perl libavahi-client3 libavahi-common-data libavahi-common3
  libavcodec58 libavformat58 libavutil56 libbdplus0 libbluray2
  libcairo-gobject2 libcairo2 libchromaprint1 libcodec2-0.8.1 libcolord2
  libcroco3 libcryptsetup12 libcrystalhd3 libcups2 libdata-dump-perl
  libdatrie1 libdbus-1-3 libdbus-glib-1-2 libdconf1 libdevmapper1.02.1
  libdrm-amdgpu1 libdrm-common libdrm-intel1 libdrm-nouveau2 libdrm-radeon1
  libdrm2 libelf1 libencode-locale-perl libepoxy0 libevent-2.1-6
  libfile-basedir-perl libfile-desktopentry-perl libfile-listing-perl
  libfile-mimeinfo-perl libflac8 libfont-afm-perl libfontconfig1 libfontenc1
  libfreetype6 libfribidi0 libgbm1 libgdk-pixbuf2.0-0 libgdk-pixbuf2.0-bin
  libgdk-pixbuf2.0-common libgl1 libgl1-mesa-dri libglapi-mesa libglib2.0-0
  libglib2.0-data libglvnd0 libglx-mesa0 libglx0 libgme0 libgomp1 libgpm2
  libgraphite2-3 libgsm1 libgtk-3-0 libgtk-3-bin libgtk-3-common
  libgudev-1.0-0 libharfbuzz0b libhtml-form-perl libhtml-format-perl
  libhtml-parser-perl libhtml-tagset-perl libhtml-tree-perl
  libhttp-cookies-perl libhttp-daemon-perl libhttp-date-perl
  libhttp-message-perl libhttp-negotiate-perl libice6 libidn11 libigdgmm5
  libimobiledevice6 libio-html-perl libio-socket-ssl-perl libio-stringy-perl
  libip4tc0 libipc-system-simple-perl libisl19 libjbig0 libjpeg62-turbo
  libjson-c3 libjson-glib-1.0-0 libjson-glib-1.0-common libjsoncpp1 libkmod2
  liblcms2-2 libllvm7 liblwp-mediatypes-perl liblwp-protocol-https-perl
  libmailtools-perl libminizip1 libmp3lame0 libmpc3 libmpfr6 libmpg123-0
  libncurses6 libnet-dbus-perl libnet-http-perl libnet-smtp-ssl-perl
  libnet-ssleay-perl libnspr4 libnss-systemd libnss3 libnuma1 libogg0
  libopenjp2-7 libopenmpt0 libopus0 libpam-systemd libpango-1.0-0
  libpangocairo-1.0-0 libpangoft2-1.0-0 libpciaccess0 libpixman-1-0 libplist3
  libpng16-16 libpolkit-agent-1-0 libpolkit-backend-1-0 libpolkit-gobject-1-0
  libprocps7 libproxy1v5 libpulse0 libre2-5 librest-0.7-0 librsvg2-2
  librsvg2-common libsensors-config libsensors5 libshine3 libsm6 libsnappy1v5
  libsndfile1 libsoup-gnome2.4-1 libsoup2.4-1 libsoxr0 libspeex1 libsqlite3-0
  libssh-gcrypt-4 libswresample3 libtext-iconv-perl libthai-data libthai0
  libtheora0 libtie-ixhash-perl libtiff5 libtimedate-perl libtry-tiny-perl
  libtwolame0 libu2f-udev libupower-glib3 liburi-perl libusb-1.0-0 libusbmuxd4
  libva-drm2 libva-x11-2 libva2 libvdpau-va-gl1 libvdpau1 libvorbis0a
  libvorbisenc2 libvorbisfile3 libvpx5 libwavpack1 libwayland-client0
  libwayland-cursor0 libwayland-egl1 libwayland-server0 libwebp6 libwebpdemux2
  libwebpmux3 libwrap0 libwww-perl libwww-robotrules-perl libx11-protocol-perl
  libx11-xcb1 libx264-155 libx265-165 libxaw7 libxcb-dri2-0 libxcb-dri3-0
  libxcb-glx0 libxcb-present0 libxcb-render0 libxcb-shape0 libxcb-shm0
  libxcb-sync1 libxcb-xfixes0 libxcomposite1 libxcursor1 libxdamage1
  libxfixes3 libxft2 libxi6 libxinerama1 libxkbcommon0 libxml-parser-perl
  libxml-twig-perl libxml-xpathengine-perl libxml2 libxmu6 libxpm4 libxrandr2
  libxrender1 libxshmfence1 libxslt1.1 libxt6 libxtst6 libxv1 libxvidcore4
  libxxf86dga1 libxxf86vm1 libzvbi-common libzvbi0 mesa-va-drivers
  mesa-vdpau-drivers notification-daemon perl-openssl-defaults policykit-1
  procps psmisc sensible-utils shared-mime-info systemd systemd-sysv ucf udev
  upower usbmuxd va-driver-all vdpau-driver-all x11-common x11-utils
  x11-xserver-utils xdg-user-dirs xdg-utils xkb-data
0 upgraded, 281 newly installed, 0 to remove and 0 not upgraded.
Need to get 222 MB of archives.
After this operation, 960 MB of additional disk space will be used.
Get:1 http://deb.debian.org/debian buster/main amd64 libapparmor1 amd64 2.13.2-10 [94.7 kB]
Get:2 http://deb.debian.org/debian buster/main amd64 libargon2-1 amd64 0~20171227-0.2 [19.6 kB]
Get:3 http://deb.debian.org/debian buster/main amd64 dmsetup amd64 2:1.02.155-3 [90.8 kB]
Get:4 http://deb.debian.org/debian buster/main amd64 libdevmapper1.02.1 amd64 2:1.02.155-3 [141 kB]
Get:5 http://deb.debian.org/debian buster/main amd64 libjson-c3 amd64 0.12.1+ds-2+deb10u1 [27.3 kB]
Get:6 http://deb.debian.org/debian buster/main amd64 libcryptsetup12 amd64 2:2.1.0-5+deb10u2 [193 kB]
Get:7 http://deb.debian.org/debian buster/main amd64 libidn11 amd64 1.33-2.2 [116 kB]
Get:8 http://deb.debian.org/debian buster/main amd64 libip4tc0 amd64 1.8.2-4 [70.2 kB]
Get:9 http://deb.debian.org/debian buster/main amd64 libkmod2 amd64 26-1 [52.7 kB]
Get:10 http://deb.debian.org/debian-security buster/updates/main amd64 systemd amd64 241-7~deb10u9 [3503 kB]
Get:11 http://deb.debian.org/debian-security buster/updates/main amd64 systemd-sysv amd64 241-7~deb10u9 [100 kB]
Get:12 http://deb.debian.org/debian-security buster/updates/main amd64 libncurses6 amd64 6.1+20181013-2+deb10u3 [102 kB]
Get:13 http://deb.debian.org/debian buster/main amd64 libprocps7 amd64 2:3.3.15-2 [61.7 kB]
Get:14 http://deb.debian.org/debian buster/main amd64 procps amd64 2:3.3.15-2 [259 kB]
Get:15 http://deb.debian.org/debian buster/main amd64 sensible-utils all 0.0.12 [15.8 kB]
Get:16 http://deb.debian.org/debian-security buster/updates/main amd64 udev amd64 241-7~deb10u9 [1278 kB]
Get:17 http://deb.debian.org/debian-security buster/updates/main amd64 libdbus-1-3 amd64 1.12.24-0+deb10u1 [218 kB]
Get:18 http://deb.debian.org/debian-security buster/updates/main amd64 dbus amd64 1.12.24-0+deb10u1 [240 kB]
Get:19 http://deb.debian.org/debian-security buster/updates/main amd64 libpam-systemd amd64 241-7~deb10u9 [209 kB]
Get:20 http://deb.debian.org/debian-security buster/updates/main amd64 libsqlite3-0 amd64 3.27.2-3+deb10u2 [641 kB]
Get:21 http://deb.debian.org/debian-security buster/updates/main amd64 libxml2 amd64 2.9.4+dfsg1-7+deb10u5 [690 kB]
Get:22 http://deb.debian.org/debian buster/main amd64 ucf all 3.0038+nmu1 [69.0 kB]
Get:23 http://deb.debian.org/debian buster/main amd64 hicolor-icon-theme all 0.17-2 [11.4 kB]
Get:24 http://deb.debian.org/debian-security buster/updates/main amd64 libglib2.0-0 amd64 2.58.3-2+deb10u4 [1259 kB]
Get:25 http://deb.debian.org/debian buster/main amd64 libjpeg62-turbo amd64 1:1.5.2-2+deb10u1 [133 kB]
Get:26 http://deb.debian.org/debian buster/main amd64 libpng16-16 amd64 1.6.36-6 [292 kB]
Get:27 http://deb.debian.org/debian buster/main amd64 libjbig0 amd64 2.1-3.1+b2 [31.0 kB]
Get:28 http://deb.debian.org/debian buster/main amd64 libwebp6 amd64 0.6.1-2+deb10u1 [261 kB]
Get:29 http://deb.debian.org/debian-security buster/updates/main amd64 libtiff5 amd64 4.1.0+git191117-2~deb10u7 [273 kB]
Get:30 http://deb.debian.org/debian buster/main amd64 shared-mime-info amd64 1.10-1 [766 kB]
Get:31 http://deb.debian.org/debian buster/main amd64 libgdk-pixbuf2.0-common all 2.38.1+dfsg-1 [316 kB]
Get:32 http://deb.debian.org/debian buster/main amd64 libgdk-pixbuf2.0-0 amd64 2.38.1+dfsg-1 [177 kB]
Get:33 http://deb.debian.org/debian buster/main amd64 gtk-update-icon-cache amd64 3.24.5-1 [81.7 kB]
Get:34 http://deb.debian.org/debian buster/main amd64 libfreetype6 amd64 2.9.1-3+deb10u3 [380 kB]
Get:35 http://deb.debian.org/debian buster/main amd64 fonts-dejavu-core all 2.37-1 [1068 kB]
Get:36 http://deb.debian.org/debian buster/main amd64 fonts-liberation all 1:1.07.4-9 [827 kB]
Get:37 http://deb.debian.org/debian buster/main amd64 fontconfig-config all 2.13.1-2 [280 kB]
Get:38 http://deb.debian.org/debian buster/main amd64 libfontconfig1 amd64 2.13.1-2 [346 kB]
Get:39 http://deb.debian.org/debian-security buster/updates/main amd64 libpixman-1-0 amd64 0.36.0-1+deb10u1 [234 kB]
Get:40 http://deb.debian.org/debian buster/main amd64 libxcb-render0 amd64 1.13.1-2 [109 kB]
Get:41 http://deb.debian.org/debian buster/main amd64 libxcb-shm0 amd64 1.13.1-2 [99.2 kB]
Get:42 http://deb.debian.org/debian buster/main amd64 libxrender1 amd64 1:0.9.10-1 [33.0 kB]
Get:43 http://deb.debian.org/debian buster/main amd64 libcairo2 amd64 1.16.0-4+deb10u1 [688 kB]
Get:44 http://deb.debian.org/debian buster/main amd64 libcroco3 amd64 0.6.12-3 [145 kB]
Get:45 http://deb.debian.org/debian buster/main amd64 fontconfig amd64 2.13.1-2 [405 kB]
Get:46 http://deb.debian.org/debian buster/main amd64 libfribidi0 amd64 1.0.5-3.1+deb10u2 [63.9 kB]
Get:47 http://deb.debian.org/debian buster/main amd64 libthai-data all 0.1.28-2 [170 kB]
Get:48 http://deb.debian.org/debian buster/main amd64 libdatrie1 amd64 0.2.12-2 [39.3 kB]
Get:49 http://deb.debian.org/debian buster/main amd64 libthai0 amd64 0.1.28-2 [54.1 kB]
Get:50 http://deb.debian.org/debian buster/main amd64 libpango-1.0-0 amd64 1.42.4-8~deb10u1 [186 kB]
Get:51 http://deb.debian.org/debian buster/main amd64 libgraphite2-3 amd64 1.3.13-7 [80.7 kB]
Get:52 http://deb.debian.org/debian buster/main amd64 libharfbuzz0b amd64 2.3.1-1 [1187 kB]
Get:53 http://deb.debian.org/debian buster/main amd64 libpangoft2-1.0-0 amd64 1.42.4-8~deb10u1 [68.3 kB]
Get:54 http://deb.debian.org/debian buster/main amd64 libpangocairo-1.0-0 amd64 1.42.4-8~deb10u1 [55.7 kB]
Get:55 http://deb.debian.org/debian buster/main amd64 librsvg2-2 amd64 2.44.10-2.1+deb10u3 [1258 kB]
Get:56 http://deb.debian.org/debian buster/main amd64 librsvg2-common amd64 2.44.10-2.1+deb10u3 [23.6 kB]
Get:57 http://deb.debian.org/debian buster/main amd64 adwaita-icon-theme all 3.30.1-1 [11.7 MB]
Get:58 http://deb.debian.org/debian buster/main amd64 libatspi2.0-0 amd64 2.30.0-7 [65.0 kB]
Get:59 http://deb.debian.org/debian buster/main amd64 libxi6 amd64 2:1.7.9-1 [82.6 kB]
Get:60 http://deb.debian.org/debian buster/main amd64 x11-common all 1:7.7+19 [251 kB]
Get:61 http://deb.debian.org/debian buster/main amd64 libxtst6 amd64 2:1.2.3-1 [27.8 kB]
Get:62 http://deb.debian.org/debian buster/main amd64 at-spi2-core amd64 2.30.0-7 [70.7 kB]
Get:63 http://deb.debian.org/debian buster/main amd64 libasound2-data all 1.1.8-1 [59.6 kB]
Get:64 http://deb.debian.org/debian buster/main amd64 libasound2 amd64 1.1.8-1 [361 kB]
Get:65 http://deb.debian.org/debian buster/main amd64 libatk1.0-data all 2.30.0-2 [145 kB]
Get:66 http://deb.debian.org/debian buster/main amd64 libatk1.0-0 amd64 2.30.0-2 [50.6 kB]
Get:67 http://deb.debian.org/debian buster/main amd64 libatk-bridge2.0-0 amd64 2.30.0-5 [61.6 kB]
Get:68 http://deb.debian.org/debian buster/main amd64 libatomic1 amd64 8.3.0-6 [9032 B]
Get:69 http://deb.debian.org/debian buster/main amd64 libaom0 amd64 1.0.0-3 [1165 kB]
Get:70 http://deb.debian.org/debian buster/main amd64 libdrm-common all 2.4.97-1 [13.8 kB]
Get:71 http://deb.debian.org/debian buster/main amd64 libdrm2 amd64 2.4.97-1 [39.7 kB]
Get:72 http://deb.debian.org/debian buster/main amd64 libva2 amd64 2.4.0-1 [57.2 kB]
Get:73 http://deb.debian.org/debian buster/main amd64 libva-drm2 amd64 2.4.0-1 [16.6 kB]
Get:74 http://deb.debian.org/debian buster/main amd64 libxfixes3 amd64 1:5.0.3-1 [21.9 kB]
Get:75 http://deb.debian.org/debian buster/main amd64 libva-x11-2 amd64 2.4.0-1 [21.3 kB]
Get:76 http://deb.debian.org/debian buster/main amd64 libvdpau1 amd64 1.1.1-10 [42.0 kB]
Get:77 http://deb.debian.org/debian-security buster/updates/main amd64 libavutil56 amd64 7:4.1.10-0+deb10u1 [246 kB]
Get:78 http://deb.debian.org/debian buster/main amd64 libcodec2-0.8.1 amd64 0.8.1-2 [233 kB]
Get:79 http://deb.debian.org/debian buster/main amd64 libcrystalhd3 amd64 1:0.0~git20110715.fdd2f19-13 [45.9 kB]
Get:80 http://deb.debian.org/debian buster/main amd64 libgsm1 amd64 1.0.18-2 [27.7 kB]
Get:81 http://deb.debian.org/debian buster/main amd64 libmp3lame0 amd64 3.100-2+b1 [367 kB]
Get:82 http://deb.debian.org/debian buster/main amd64 libopenjp2-7 amd64 2.3.0-2+deb10u2 [158 kB]
Get:83 http://deb.debian.org/debian buster/main amd64 libopus0 amd64 1.3-1 [191 kB]
Get:84 http://deb.debian.org/debian buster/main amd64 libshine3 amd64 3.1.1-2 [23.6 kB]
Get:85 http://deb.debian.org/debian buster/main amd64 libsnappy1v5 amd64 1.1.7-1 [17.0 kB]
Get:86 http://deb.debian.org/debian buster/main amd64 libspeex1 amd64 1.2~rc1.2-1+b2 [54.8 kB]
Get:87 http://deb.debian.org/debian buster/main amd64 libgomp1 amd64 8.3.0-6 [75.8 kB]
Get:88 http://deb.debian.org/debian buster/main amd64 libsoxr0 amd64 0.1.2-3 [65.9 kB]
Get:89 http://deb.debian.org/debian-security buster/updates/main amd64 libswresample3 amd64 7:4.1.10-0+deb10u1 [113 kB]
Get:90 http://deb.debian.org/debian buster/main amd64 libogg0 amd64 1.3.2-1+b1 [21.3 kB]
Get:91 http://deb.debian.org/debian buster/main amd64 libtheora0 amd64 1.1.1+dfsg.1-15 [169 kB]
Get:92 http://deb.debian.org/debian buster/main amd64 libtwolame0 amd64 0.3.13-4 [49.3 kB]
Get:93 http://deb.debian.org/debian buster/main amd64 libvorbis0a amd64 1.3.6-2 [93.5 kB]
Get:94 http://deb.debian.org/debian buster/main amd64 libvorbisenc2 amd64 1.3.6-2 [80.2 kB]
Get:95 http://deb.debian.org/debian buster/main amd64 libvpx5 amd64 1.7.0-3+deb10u1 [800 kB]
Get:96 http://deb.debian.org/debian buster/main amd64 libwavpack1 amd64 5.1.0-6+deb10u1 [86.9 kB]
Get:97 http://deb.debian.org/debian buster/main amd64 libwebpmux3 amd64 0.6.1-2+deb10u1 [97.8 kB]
Get:98 http://deb.debian.org/debian buster/main amd64 libx264-155 amd64 2:0.155.2917+git0a84d98-2 [528 kB]
Get:99 http://deb.debian.org/debian buster/main amd64 libnuma1 amd64 2.0.12-1 [26.2 kB]
Get:100 http://deb.debian.org/debian buster/main amd64 libx265-165 amd64 2.9-4 [1041 kB]
Get:101 http://deb.debian.org/debian buster/main amd64 libxvidcore4 amd64 2:1.3.5-1 [241 kB]
Get:102 http://deb.debian.org/debian buster/main amd64 libzvbi-common all 0.2.35-16 [64.3 kB]
Get:103 http://deb.debian.org/debian buster/main amd64 libzvbi0 amd64 0.2.35-16 [270 kB]
Get:104 http://deb.debian.org/debian-security buster/updates/main amd64 libavcodec58 amd64 7:4.1.10-0+deb10u1 [4812 kB]
Get:105 http://deb.debian.org/debian-security buster/updates/main amd64 libbluray2 amd64 1:1.1.0-1+deb10u1 [146 kB]
Get:106 http://deb.debian.org/debian buster/main amd64 libchromaprint1 amd64 1.4.3-3 [41.4 kB]
Get:107 http://deb.debian.org/debian buster/main amd64 libgme0 amd64 0.6.2-1 [121 kB]
Get:108 http://deb.debian.org/debian buster/main amd64 libmpg123-0 amd64 1.25.10-2 [140 kB]
Get:109 http://deb.debian.org/debian buster/main amd64 libvorbisfile3 amd64 1.3.6-2 [25.5 kB]
Get:110 http://deb.debian.org/debian buster/main amd64 libopenmpt0 amd64 0.4.3-1+deb10u1 [627 kB]
Get:111 http://deb.debian.org/debian buster/main amd64 libssh-gcrypt-4 amd64 0.8.7-1+deb10u1 [200 kB]
Get:112 http://deb.debian.org/debian-security buster/updates/main amd64 libavformat58 amd64 7:4.1.10-0+deb10u1 [1020 kB]
Get:113 http://deb.debian.org/debian buster/main amd64 libavahi-common-data amd64 0.7-4+deb10u1 [122 kB]
Get:114 http://deb.debian.org/debian buster/main amd64 libavahi-common3 amd64 0.7-4+deb10u1 [54.4 kB]
Get:115 http://deb.debian.org/debian buster/main amd64 libavahi-client3 amd64 0.7-4+deb10u1 [58.2 kB]
Get:116 http://deb.debian.org/debian buster/main amd64 libcups2 amd64 2.2.10-6+deb10u6 [325 kB]
Get:117 http://deb.debian.org/debian buster/main amd64 libevent-2.1-6 amd64 2.1.8-stable-4 [177 kB]
Get:118 http://deb.debian.org/debian buster/main amd64 libflac8 amd64 1.3.2-3+deb10u2 [193 kB]
Get:119 http://deb.debian.org/debian buster/main amd64 libwayland-server0 amd64 1.16.0-1 [32.5 kB]
Get:120 http://deb.debian.org/debian buster/main amd64 libgbm1 amd64 18.3.6-2+deb10u1 [67.8 kB]
Get:121 http://deb.debian.org/debian buster/main amd64 libcairo-gobject2 amd64 1.16.0-4+deb10u1 [125 kB]
Get:122 http://deb.debian.org/debian buster/main amd64 liblcms2-2 amd64 2.9-3 [145 kB]
Get:123 http://deb.debian.org/debian buster/main amd64 libcolord2 amd64 1.4.3-4 [141 kB]
Get:124 http://deb.debian.org/debian buster/main amd64 libepoxy0 amd64 1.5.3-0.1 [190 kB]
Get:125 http://deb.debian.org/debian buster/main amd64 libjson-glib-1.0-common all 1.4.4-2 [52.3 kB]
Get:126 http://deb.debian.org/debian buster/main amd64 libjson-glib-1.0-0 amd64 1.4.4-2 [61.2 kB]
Get:127 http://deb.debian.org/debian buster/main amd64 libproxy1v5 amd64 0.4.15-5+deb10u1 [56.1 kB]
Get:128 http://deb.debian.org/debian buster/main amd64 glib-networking-common all 2.58.0-2+deb10u2 [59.5 kB]
Get:129 http://deb.debian.org/debian buster/main amd64 glib-networking-services amd64 2.58.0-2+deb10u2 [13.7 kB]
Get:130 http://deb.debian.org/debian buster/main amd64 libdconf1 amd64 0.30.1-2 [40.7 kB]
Get:131 http://deb.debian.org/debian-security buster/updates/main amd64 dbus-user-session amd64 1.12.24-0+deb10u1 [97.9 kB]
Get:132 http://deb.debian.org/debian buster/main amd64 dconf-service amd64 0.30.1-2 [36.4 kB]
Get:133 http://deb.debian.org/debian buster/main amd64 dconf-gsettings-backend amd64 0.30.1-2 [28.9 kB]
Get:134 http://deb.debian.org/debian buster/main amd64 gsettings-desktop-schemas all 3.28.1-1 [529 kB]
Get:135 http://deb.debian.org/debian buster/main amd64 glib-networking amd64 2.58.0-2+deb10u2 [54.6 kB]
Get:136 http://deb.debian.org/debian buster/main amd64 libsoup2.4-1 amd64 2.64.2-2 [253 kB]
Get:137 http://deb.debian.org/debian buster/main amd64 libsoup-gnome2.4-1 amd64 2.64.2-2 [18.0 kB]
Get:138 http://deb.debian.org/debian buster/main amd64 librest-0.7-0 amd64 0.8.1-1 [33.7 kB]
Get:139 http://deb.debian.org/debian buster/main amd64 libwayland-client0 amd64 1.16.0-1 [26.4 kB]
Get:140 http://deb.debian.org/debian buster/main amd64 libwayland-cursor0 amd64 1.16.0-1 [14.1 kB]
Get:141 http://deb.debian.org/debian buster/main amd64 libwayland-egl1 amd64 1.16.0-1 [8204 B]
Get:142 http://deb.debian.org/debian buster/main amd64 libxcomposite1 amd64 1:0.4.4-2 [16.5 kB]
Get:143 http://deb.debian.org/debian buster/main amd64 libxcursor1 amd64 1:1.1.15-2 [36.6 kB]
Get:144 http://deb.debian.org/debian buster/main amd64 libxdamage1 amd64 1:1.1.4-3+b3 [14.9 kB]
Get:145 http://deb.debian.org/debian buster/main amd64 libxinerama1 amd64 2:1.1.4-2 [17.7 kB]
Get:146 http://deb.debian.org/debian buster/main amd64 xkb-data all 2.26-2 [681 kB]
Get:147 http://deb.debian.org/debian buster/main amd64 libxkbcommon0 amd64 0.8.2-1 [123 kB]
Get:148 http://deb.debian.org/debian buster/main amd64 libxrandr2 amd64 2:1.5.1-1 [37.5 kB]
Get:149 http://deb.debian.org/debian buster/main amd64 libgtk-3-common all 3.24.5-1 [3678 kB]
Get:150 http://deb.debian.org/debian buster/main amd64 libgtk-3-0 amd64 3.24.5-1 [2580 kB]
Get:151 http://deb.debian.org/debian buster/main amd64 libjsoncpp1 amd64 1.7.4-3 [75.6 kB]
Get:152 http://deb.debian.org/debian buster/main amd64 libminizip1 amd64 1.1-8+b1 [20.4 kB]
Get:153 http://deb.debian.org/debian buster/main amd64 libnspr4 amd64 2:4.20-1 [112 kB]
Get:154 http://deb.debian.org/debian-security buster/updates/main amd64 libnss3 amd64 2:3.42.1-1+deb10u6 [1245 kB]
Get:155 http://deb.debian.org/debian buster/main amd64 libasyncns0 amd64 0.8-6 [12.5 kB]
Get:156 http://deb.debian.org/debian buster/main amd64 libice6 amd64 2:1.0.9-2 [58.7 kB]
Get:157 http://deb.debian.org/debian buster/main amd64 libsm6 amd64 2:1.2.3-1 [35.1 kB]
Get:158 http://deb.debian.org/debian-security buster/updates/main amd64 libsndfile1 amd64 1.0.28-6+deb10u2 [253 kB]
Get:159 http://deb.debian.org/debian buster/main amd64 libwrap0 amd64 7.6.q-28 [58.7 kB]
Get:160 http://deb.debian.org/debian buster/main amd64 libx11-xcb1 amd64 2:1.6.7-1+deb10u2 [191 kB]
Get:161 http://deb.debian.org/debian buster/main amd64 libpulse0 amd64 12.2-4+deb10u1 [281 kB]
Get:162 http://deb.debian.org/debian buster/main amd64 libre2-5 amd64 20190101+dfsg-2 [161 kB]
Get:163 http://deb.debian.org/debian buster/main amd64 libwebpdemux2 amd64 0.6.1-2+deb10u1 [87.6 kB]
Get:164 http://deb.debian.org/debian buster/main amd64 libxshmfence1 amd64 1.3-1 [8820 B]
Get:165 http://deb.debian.org/debian-security buster/updates/main amd64 libxslt1.1 amd64 1.1.32-2.2~deb10u2 [236 kB]
Get:166 http://deb.debian.org/debian buster/main amd64 libfontenc1 amd64 1:1.1.3-1+b2 [24.4 kB]
Get:167 http://deb.debian.org/debian buster/main amd64 libglvnd0 amd64 1.1.0-1 [48.6 kB]
Get:168 http://deb.debian.org/debian buster/main amd64 libglapi-mesa amd64 18.3.6-2+deb10u1 [66.3 kB]
Get:169 http://deb.debian.org/debian buster/main amd64 libxcb-dri2-0 amd64 1.13.1-2 [101 kB]
Get:170 http://deb.debian.org/debian buster/main amd64 libxcb-dri3-0 amd64 1.13.1-2 [100 kB]
Get:171 http://deb.debian.org/debian buster/main amd64 libxcb-glx0 amd64 1.13.1-2 [116 kB]
Get:172 http://deb.debian.org/debian buster/main amd64 libxcb-present0 amd64 1.13.1-2 [99.1 kB]
Get:173 http://deb.debian.org/debian buster/main amd64 libxcb-sync1 amd64 1.13.1-2 [103 kB]
Get:174 http://deb.debian.org/debian buster/main amd64 libxxf86vm1 amd64 1:1.1.4-1+b2 [20.8 kB]
Get:175 http://deb.debian.org/debian buster/main amd64 libdrm-amdgpu1 amd64 2.4.97-1 [27.3 kB]
Get:176 http://deb.debian.org/debian buster/main amd64 libpciaccess0 amd64 0.14-1 [53.5 kB]
Get:177 http://deb.debian.org/debian buster/main amd64 libdrm-intel1 amd64 2.4.97-1 [69.8 kB]
Get:178 http://deb.debian.org/debian buster/main amd64 libdrm-nouveau2 amd64 2.4.97-1 [26.3 kB]
Get:179 http://deb.debian.org/debian buster/main amd64 libdrm-radeon1 amd64 2.4.97-1 [31.1 kB]
Get:180 http://deb.debian.org/debian buster/main amd64 libelf1 amd64 0.176-1.1 [161 kB]
Get:181 http://deb.debian.org/debian buster/main amd64 libllvm7 amd64 1:7.0.1-8+deb10u2 [13.1 MB]
Get:182 http://deb.debian.org/debian buster/main amd64 libsensors-config all 1:3.5.0-3 [31.6 kB]
Get:183 http://deb.debian.org/debian buster/main amd64 libsensors5 amd64 1:3.5.0-3 [52.6 kB]
Get:184 http://deb.debian.org/debian buster/main amd64 libgl1-mesa-dri amd64 18.3.6-2+deb10u1 [6685 kB]
Get:185 http://deb.debian.org/debian buster/main amd64 libglx-mesa0 amd64 18.3.6-2+deb10u1 [180 kB]
Get:186 http://deb.debian.org/debian buster/main amd64 libglx0 amd64 1.1.0-1 [30.0 kB]
Get:187 http://deb.debian.org/debian buster/main amd64 libgl1 amd64 1.1.0-1 [91.1 kB]
Get:188 http://deb.debian.org/debian buster/main amd64 libxt6 amd64 1:1.1.5-1+b3 [190 kB]
Get:189 http://deb.debian.org/debian buster/main amd64 libxmu6 amd64 2:1.1.2-2+b3 [60.8 kB]
Get:190 http://deb.debian.org/debian buster/main amd64 libxpm4 amd64 1:3.5.12-1 [49.1 kB]
Get:191 http://deb.debian.org/debian buster/main amd64 libxaw7 amd64 2:1.0.13-1+b2 [201 kB]
Get:192 http://deb.debian.org/debian buster/main amd64 libxcb-shape0 amd64 1.13.1-2 [99.5 kB]
Get:193 http://deb.debian.org/debian buster/main amd64 libxft2 amd64 2.3.2-2 [57.2 kB]
Get:194 http://deb.debian.org/debian buster/main amd64 libxv1 amd64 2:1.0.11-1 [24.6 kB]
Get:195 http://deb.debian.org/debian buster/main amd64 libxxf86dga1 amd64 2:1.1.4-1+b3 [22.1 kB]
Get:196 http://deb.debian.org/debian buster/main amd64 x11-utils amd64 7.7+4 [202 kB]
Get:197 http://deb.debian.org/debian buster/main amd64 xdg-utils all 1.1.3-1+deb10u1 [73.7 kB]
Get:198 http://deb.debian.org/debian buster/main amd64 chromium-common amd64 90.0.4430.212-1~deb10u1 [1423 kB]
Get:199 http://deb.debian.org/debian buster/main amd64 chromium amd64 90.0.4430.212-1~deb10u1 [58.3 MB]
Get:200 http://deb.debian.org/debian buster/main amd64 chromium-sandbox amd64 90.0.4430.212-1~deb10u1 [146 kB]
Get:201 http://deb.debian.org/debian buster/main amd64 libisl19 amd64 0.20-2 [587 kB]
Get:202 http://deb.debian.org/debian buster/main amd64 libmpfr6 amd64 4.0.2-1 [775 kB]
Get:203 http://deb.debian.org/debian buster/main amd64 libmpc3 amd64 1.1.0-1 [41.3 kB]
Get:204 http://deb.debian.org/debian buster/main amd64 cpp-8 amd64 8.3.0-6 [8914 kB]
Get:205 http://deb.debian.org/debian buster/main amd64 cpp amd64 4:8.3.0-1 [19.4 kB]
Get:206 http://deb.debian.org/debian buster/main amd64 libdbus-glib-1-2 amd64 0.110-4 [71.5 kB]
Get:207 http://deb.debian.org/debian-security buster/updates/main amd64 firefox-esr amd64 102.10.0esr-1~deb10u1 [59.6 MB]
Get:208 http://deb.debian.org/debian buster/main amd64 i965-va-driver amd64 2.3.0+dfsg1-1 [311 kB]
Get:209 http://deb.debian.org/debian buster/main amd64 libigdgmm5 amd64 18.4.1+ds1-1 [78.7 kB]
Get:210 http://deb.debian.org/debian buster/main amd64 intel-media-va-driver amd64 18.4.1+dfsg1-1 [974 kB]
Get:211 http://deb.debian.org/debian buster/main amd64 libaacs0 amd64 0.9.0-2 [52.4 kB]
Get:212 http://deb.debian.org/debian buster/main amd64 libauthen-sasl-perl all 2.1600-1 [50.8 kB]
Get:213 http://deb.debian.org/debian buster/main amd64 libbdplus0 amd64 0.1.2-3 [47.5 kB]
Get:214 http://deb.debian.org/debian buster/main amd64 libdata-dump-perl all 1.23-1 [29.5 kB]
Get:215 http://deb.debian.org/debian buster/main amd64 libencode-locale-perl all 1.05-1 [13.7 kB]
Get:216 http://deb.debian.org/debian buster/main amd64 libipc-system-simple-perl all 1.25-4 [26.5 kB]
Get:217 http://deb.debian.org/debian buster/main amd64 libfile-basedir-perl all 0.08-1 [17.7 kB]
Get:218 http://deb.debian.org/debian buster/main amd64 liburi-perl all 1.76-1 [89.9 kB]
Get:219 http://deb.debian.org/debian buster/main amd64 libfile-desktopentry-perl all 0.22-1 [19.2 kB]
Get:220 http://deb.debian.org/debian buster/main amd64 libtimedate-perl all 2.3000-2+deb10u1 [38.1 kB]
Get:221 http://deb.debian.org/debian buster/main amd64 libhttp-date-perl all 6.02-1 [10.7 kB]
Get:222 http://deb.debian.org/debian buster/main amd64 libfile-listing-perl all 6.04-1 [10.3 kB]
Get:223 http://deb.debian.org/debian buster/main amd64 libfile-mimeinfo-perl all 0.29-1 [46.5 kB]
Get:224 http://deb.debian.org/debian buster/main amd64 libfont-afm-perl all 1.20-2 [13.6 kB]
Get:225 http://deb.debian.org/debian buster/main amd64 libgdk-pixbuf2.0-bin amd64 2.38.1+dfsg-1 [24.1 kB]
Get:226 http://deb.debian.org/debian-security buster/updates/main amd64 libglib2.0-data all 2.58.3-2+deb10u4 [1110 kB]
Get:227 http://deb.debian.org/debian buster/main amd64 libgpm2 amd64 1.20.7-5 [35.1 kB]
Get:228 http://deb.debian.org/debian buster/main amd64 libgtk-3-bin amd64 3.24.5-1 [114 kB]
Get:229 http://deb.debian.org/debian buster/main amd64 libgudev-1.0-0 amd64 232-2 [13.6 kB]
Get:230 http://deb.debian.org/debian buster/main amd64 libhtml-tagset-perl all 3.20-3 [12.7 kB]
Get:231 http://deb.debian.org/debian buster/main amd64 libhtml-parser-perl amd64 3.72-3+b3 [105 kB]
.............
Setting up librest-0.7-0:amd64 (0.8.1-1) ...
Setting up libgtk-3-0:amd64 (3.24.5-1) ...
Setting up libgtk-3-bin (3.24.5-1) ...
Setting up notification-daemon (3.20.0-4) ...
Setting up chromium (90.0.4430.212-1~deb10u1) ...
update-alternatives: using /usr/bin/chromium to provide /usr/bin/x-www-browser (x-www-browser) in auto mode
update-alternatives: using /usr/bin/chromium to provide /usr/bin/gnome-www-browser (gnome-www-browser) in auto mode
Setting up firefox-esr (102.10.0esr-1~deb10u1) ...
update-alternatives: using /usr/bin/firefox-esr to provide /usr/bin/x-www-browser (x-www-browser) in auto mode
update-alternatives: warning: skip creation of /usr/share/man/man1/x-www-browser.1.gz because associated file /usr/share/man/man1/firefox-esr.1.gz (of link group x-www-browser) doesn't exist
update-alternatives: using /usr/bin/firefox-esr to provide /usr/bin/gnome-www-browser (gnome-www-browser) in auto mode
update-alternatives: warning: skip creation of /usr/share/man/man1/gnome-www-browser.1.gz because associated file /usr/share/man/man1/firefox-esr.1.gz (of link group gnome-www-browser) doesn't exist
Setting up liblwp-protocol-https-perl (6.07-2) ...
Setting up libwww-perl (6.36-2) ...
Setting up libxml-parser-perl (2.44-4) ...
Setting up libxml-twig-perl (1:3.50-1.1) ...
Setting up libnet-dbus-perl (1.1.0-5+b1) ...
Processing triggers for systemd (241-7~deb10u9) ...
Processing triggers for libc-bin (2.28-10+deb10u2) ...
Processing triggers for libgdk-pixbuf2.0-0:amd64 (2.38.1+dfsg-1) ...
Requesting token at 'https://api.github.com/repos/ajaychinthapalli/github-action-runners/actions/runners/registration-token'

--------------------------------------------------------------------------------
|        ____ _ _   _   _       _          _        _   _                      |
|       / ___(_) |_| | | |_   _| |__      / \   ___| |_(_) ___  _ __  ___      |
|      | |  _| | __| |_| | | | | '_ \    / _ \ / __| __| |/ _ \| '_ \/ __|     |
|      | |_| | | |_|  _  | |_| | |_) |  / ___ \ (__| |_| | (_) | | | \__ \     |
|       \____|_|\__|_| |_|\__,_|_.__/  /_/   \_\___|\__|_|\___/|_| |_|___/     |
|                                                                              |
|                       Self-hosted runner registration                        |
|                                                                              |
--------------------------------------------------------------------------------

# Authentication


√ Connected to GitHub

# Runner Registration




√ Runner successfully added
√ Runner connection is good

# Runner settings


√ Settings Saved.

.path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
Starting Runner listener with startup type: service
Started listener process, pid: 3606
Started running service

√ Connected to GitHub

Current runner version: '2.303.0'
2023-04-19 03:59:06Z: Listening for Jobs

```

## Configuring self-hosted runners in kubernetes

```txt
bash-5.2$ minikube start
😄  minikube v1.27.1 on Darwin 10.13.6
🎉  minikube 1.30.1 is available! Download it: https://github.com/kubernetes/minikube/releases/tag/v1.30.1
💡  To disable this notice, run: 'minikube config set WantUpdateNotification false'

✨  Using the docker driver based on existing profile
💨  For improved Docker Desktop performance, Upgrade Docker Desktop to a newer version (Minimum recommended version is 20.10.0, minimum supported version is 18.09.0, current version is 19.03.13)
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
🔄  Restarting existing docker container for "minikube" ...
🐳  Preparing Kubernetes v1.25.2 on Docker 20.10.18 ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

bash-5.2$ kubectl get nodes
NAME       STATUS   ROLES           AGE    VERSION
minikube   Ready    control-plane   114d   v1.25.2

bash-5.2$ docker ps
CONTAINER ID        IMAGE                                 COMMAND                  CREATED             STATUS              PORTS                                                                                                                        NAMES
f93590b4cb4f        gcr.io/k8s-minikube/kicbase:v0.0.35   "/usr/local/bin/entr…"   3 months ago        Up 3 minutes        0.0.0.0:32772->22/tcp, 0.0.0.0:32771->2376/tcp, 0.0.0.0:32770->5000/tcp, 0.0.0.0:32769->8443/tcp, 0.0.0.0:32768->32443/tcp   minikube

bash-5.2$ docker run -d -p 5000:5000 --restart=always --name registry registry:2
Unable to find image 'registry:2' locally
2: Pulling from library/registry
91d30c5bc195: Pull complete
65d52c8ad3c4: Pull complete
54f80cd081c9: Pull complete
ca8951d7f653: Pull complete
5ee46e9ce9b6: Pull complete
Digest: sha256:8c51be2f669c82da8015017ff1eae5e5155fcf707ba914c5c7b798fbeb03b50c
Status: Downloaded newer image for registry:2
61bba9d494a1156c757c123ff3302ebd4613cb292667a2e19744a2171596ca6d

bash-5.2$ docker build . --tag github-action-runners
[+] Building 2.6s (11/11) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                                                                   0.3s
 => => transferring dockerfile: 37B                                                                                                                                                                    0.0s
 => [internal] load .dockerignore                                                                                                                                                                      0.3s
 => => transferring context: 2B                                                                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/debian:buster-slim                                                                                                                                  1.7s
 => [internal] load build context                                                                                                                                                                      0.2s
 => => transferring context: 64B                                                                                                                                                                       0.1s
 => [1/6] FROM docker.io/library/debian:buster-slim@sha256:4d208d79338beaa197912496d0791921a31d9c58fa6fbd299787fab37cc893dd                                                                            0.0s
 => CACHED [2/6] RUN apt-get update     && apt-get install -y         curl         sudo         git         jq         iputils-ping     && apt-get clean     && rm -rf /var/lib/apt/lists/*     && us  0.0s
 => CACHED [3/6] WORKDIR /home/github                                                                                                                                                                  0.0s
 => CACHED [4/6] RUN GITHUB_RUNNER_VERSION=$(curl --silent "https://api.github.com/repos/actions/runner/releases/latest" | jq -r '.tag_name[1:]')     && curl -Ls https://github.com/actions/runner/r  0.0s
 => CACHED [5/6] COPY --chown=github:github entrypoint.sh runsvc.sh ./                                                                                                                                 0.0s
 => CACHED [6/6] RUN sudo chmod u+x ./entrypoint.sh ./runsvc.sh                                                                                                                                        0.0s
 => exporting to image                                                                                                                                                                                 0.0s
 => => exporting layers                                                                                                                                                                                0.0s
 => => writing image sha256:5bbba2c3a709fab922c10f395c8f173eb711cbe7b7389a45f09a8112463541eb                                                                                                           0.0s
 => => naming to docker.io/library/github-action-runners                                                                                                                                               0.0s

bash-5.2$ docker images
REPOSITORY                             TAG                                              IMAGE ID            CREATED             SIZE
github-action-runners                  latest                                           5bbba2c3a709        About an hour ago   615MB
registry                               2                                                8db46f9d7550        2 weeks ago         24.2MB
<none>                                 <none>                                           06dd076698f5        2 months ago        56.9MB
httpd                                  2.4-alpine                                       ab426eae6a38        3 months ago        56.9MB
gcr.io/k8s-minikube/kicbase            v0.0.35                                          7fb60d0ea30e        6 months ago        1.12GB
docker/desktop-kubernetes              kubernetes-v1.19.3-cni-v0.8.5-critools-v1.17.0   7f85afe431d8        2 years ago         285MB
k8s.gcr.io/kube-proxy                  v1.19.3                                          cdef7632a242        2 years ago         118MB
k8s.gcr.io/kube-controller-manager     v1.19.3                                          9b60aca1d818        2 years ago         111MB
k8s.gcr.io/kube-scheduler              v1.19.3                                          aaefbfa906bd        2 years ago         45.7MB
k8s.gcr.io/kube-apiserver              v1.19.3                                          a301be0cd44b        2 years ago         119MB
k8s.gcr.io/etcd                        3.4.13-0                                         0369cf4303ff        2 years ago         253MB
k8s.gcr.io/coredns                     1.7.0                                            bfe3a36ebd25        2 years ago         45.2MB
docker/desktop-storage-provisioner     v1.1                                             e704287ce753        3 years ago         41.8MB
docker/desktop-vpnkit-controller       v1.0                                             79da37e5a3aa        3 years ago         36.6MB
k8s.gcr.io/pause                       3.2                                              80d28bedfe5d        3 years ago         683kB

bash-5.2$ kubectl create -f deployment.yml
deployment.apps/github-action-runners created

bash-5.2$ minikube dashboard
🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
🎉  Opening http://127.0.0.1:50878/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
```
![workloads.png](images%2Fworkloads.png)
![runners.png](images%2Frunners.png)