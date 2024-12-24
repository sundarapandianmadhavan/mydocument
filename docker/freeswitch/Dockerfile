# Use Debian 12 as the base image
FROM debian:12

# Install necessary tools and dependencies
RUN apt-get update && \
    apt-get install -yq gnupg2 wget lsb-release && \
    rm -rf /var/lib/apt/lists/*

# Download SignalWire GPG key and add repository
RUN wget --http-user=signalwire --http-password=pat_9RHJx4nPSUwDBYbNny15NTvR -O /usr/share/keyrings/signalwire-freeswitch-repo.gpg https://freeswitch.signalwire.com/repo/deb/debian-release/signalwire-freeswitch-repo.gpg && \
    echo "machine freeswitch.signalwire.com login signalwire password pat_9RHJx4nPSUwDBYbNny15NTvR" > /etc/apt/auth.conf && \
    echo "deb [signed-by=/usr/share/keyrings/signalwire-freeswitch-repo.gpg] https://freeswitch.signalwire.com/repo/deb/debian-release/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/freeswitch.list && \
    echo "deb-src [signed-by=/usr/share/keyrings/signalwire-freeswitch-repo.gpg] https://freeswitch.signalwire.com/repo/deb/debian-release/ $(lsb_release -sc) main" >> /etc/apt/sources.list.d/freeswitch.list && \
    apt-get update

# Install build dependencies
RUN apt-get build-dep -y freeswitch
RUN apt-get install nmap nano -y
# Clone FreeSWITCH source code
RUN apt-get install -yq git && \
    cd /usr/src/ && \
    git clone https://github.com/signalwire/freeswitch.git -bv1.10 freeswitch && \
    chmod 777 -R /usr/src/freeswitch/libs/

# Build and install FreeSWITCH
#RUN cd /usr/src/freeswitch && \
#    ./bootstrap.sh -j && \
#    ./configure && \
#    make && \
#    make install && \
#    make all cd-sounds-install cd-moh-install && \
#    make mod_v8-install && \ # Install mod_v8 module
#    ln -s /usr/local/freeswitch/bin/freeswitch /usr/bin/freeswitch && \
#    ln -s /usr/local/freeswitch/bin/fs_cli /usr/bin/fs_cli

# Build and install FreeSWITCH
RUN cd /usr/src/freeswitch && \
    ./bootstrap.sh -j && \
    ./configure && \
    make && \
    make install && \
    make all cd-sounds-install cd-moh-install && \
    make mod_v8-install && \
    ln -s /usr/local/freeswitch/bin/freeswitch /usr/bin/freeswitch && \
    ln -s /usr/local/freeswitch/bin/fs_cli /usr/bin/fs_cli




# Expose all required ports for FreeSWITCH
EXPOSE 5060/tcp 5060/udp 5061/tcp 5061/udp 16384-32768/udp 8021/tcp 8080/tcp

# Configure Event Socket to listen on all interfaces
RUN sed -i 's/<param name="listen-ip" value="::"\/>/<param name="listen-ip" value="0.0.0.0"\/>/' /usr/local/freeswitch/conf/autoload_configs/event_socket.conf.xml && \
    sed -i 's/<param name="listen-port" value="8021"\/>/<param name="listen-port" value="8021"\/>/' /usr/local/freeswitch/conf/autoload_configs/event_socket.conf.xml

# Start FreeSWITCH with Event Socket enabled
CMD ["/usr/bin/freeswitch", "-nonat"]


#Volume with nfs
docker volume create --driver local --name db_data --opt type=nfs --opt device=:<nfs location> --opt o=addr=<nfs ip>,rw,nolock