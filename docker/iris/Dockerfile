# Heavily based on the original Iris Dockerfile here: https://github.com/jaedb/Iris/blob/master/Dockerfile
FROM arm32v7/debian:buster-slim

# Switch to the root user while we do our changes
USER root

# Install GStreamer and other required Debian packages
RUN apt-get update \
  && apt-get install -y \
    dumb-init \
    graphviz-dev \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-pulseaudio \
    libasound2-dev \
    python-dev \
    python-gst-1.0 \
    python3-gst-1.0 \
    python3-gi \
    python3-cffi \
    python3-pip \
    wget \
    gnupg2 \
    git \
    sudo \
    dumb-init \
  && rm -rf /var/lib/apt/lists/*

# Install libspotify-dev from apt.mopidy.com
RUN wget -q -O - https://apt.mopidy.com/mopidy.gpg \
  | APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=DontWarn apt-key add - \
  && wget -q -O /etc/apt/sources.list.d/mopidy.list https://apt.mopidy.com/buster.list \
  && apt-get update \
  && apt-get install -y libspotify-dev \
  && rm -rf /var/lib/apt/lists/*

# Clone Iris from the repository and install in development mode.
# This allows a binding at "/iris" to map to your local folder for development, rather than
# installing using pip.
RUN git clone https://github.com/jaedb/Iris.git /iris \
 && cd /iris \
 && git checkout 3.47.0 \
 && python3 setup.py develop \
 && mkdir -p /var/lib/mopidy/.config \
 && ln -s /config /var/lib/mopidy/.config/mopidy \
 # Allow mopidy user to run system commands (restart, local scan, etc)
 && echo "mopidy ALL=NOPASSWD: /iris/mopidy_iris/system.sh" >> /etc/sudoers

# Install additional Python dependencies
RUN python3 -m pip install --no-cache tox mopidy-mpd mopidy-spotify mopidy-local

# Start helper script.
RUN cp /iris/docker/entrypoint.sh /entrypoint.sh

# Default configuration.
RUN mkdir /config && cp /iris/docker/mopidy.example.conf /config/mopidy.conf

# Copy the pulse-client configuratrion.
RUN cp /iris/docker/pulse-client.conf /etc/pulse/client.conf

# Add version info to image
RUN cp /iris/VERSION /

# Allows any user to run mopidy, but runs by default as a randomly generated UID/GID.
RUN useradd -ms /bin/bash mopidy
ENV HOME=/var/lib/mopidy
RUN set -ex \
 && usermod -G audio,sudo mopidy \
 && mkdir /var/lib/mopidy/local \
 && chown mopidy:audio -R $HOME /entrypoint.sh /iris \
 && chmod go+rwx -R $HOME /entrypoint.sh /iris \
 && echo "1" >> /IS_CONTAINER

# Runs as mopidy user by default.
USER mopidy:audio

VOLUME ["/var/lib/mopidy/local"]

EXPOSE 6600 6680 1704 1705 5555/udp

ENTRYPOINT ["/usr/bin/dumb-init", "/entrypoint.sh"]
CMD ["mopidy"]

