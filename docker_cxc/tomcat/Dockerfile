# Use Debian as the base image
FROM debian:12

# Set working directory
WORKDIR /opt

# Install the default JDK and other necessary tools
RUN apt-get update && apt-get install -y \
    default-jdk \
    unzip \
    curl \
    nano \
    perl \
    nmap \
    cpanminus \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*  # Clean up APT when done

RUN perl -MCPAN -e 'install DBI'
RUN perl -MCPAN -e 'install DBD::Pg'

# Download and install Apache Tomcat 9
# Copying the pre-downloaded Tomcat file (apache-tomcat-9.0.78.tar.gz) in /opt directory
COPY apache-tomcat-9.0.78.tar.gz /opt

#RUN perl -MCPAN -e 'install DBI'
#RUN perl -MCPAN -e 'install DBD::Pg'

COPY cxc /usr/local/
# Un-tar the Tomcat file in the /opt directory
RUN tar xzf apache-tomcat-9.0.78.tar.gz && \
    rm -f apache-tomcat-9.0.78.tar.gz

# Change permissions and ownership
#RUN chmod -R 777 /opt/apache-tomcat-9.0.78/* && \
#    chown -R root:root /opt/apache-tomcat-9.0.78 && \
#    chmod a+x /opt/apache-tomcat-9.0.78/bin/catalina.sh

#RUN chmod -R 755 /opt/apache-tomcat-9.0.78 && \
#    chmod +x /opt/apache-tomcat-9.0.78/bin/*.sh

RUN chmod -R 755 /opt/apache-tomcat-9.0.78 && \
    chmod +x /opt/apache-tomcat-9.0.78/bin/*.sh && \
    chmod -R 755 /opt/apache-tomcat-9.0.78/conf && \
    chmod -R 755 /opt/apache-tomcat-9.0.78/webapps && \
    chmod -R 755 /opt/apache-tomcat-9.0.78/logs && \
    chmod -R 755 /opt/apache-tomcat-9.0.78/temp && \
    chmod -R 755 /opt/apache-tomcat-9.0.78/work && \
    chown -R root:root /opt/apache-tomcat-9.0.78


# Configure Tomcat users for admin access
RUN echo '\
<tomcat-users>\n\
  <role rolename="manager-gui"/>\n\
  <role rolename="admin-gui"/>\n\
  <user username="admin" password="P@ssw0rd" roles="manager-gui,admin-gui"/>\n\
</tomcat-users>' > /opt/apache-tomcat-9.0.78/conf/tomcat-users.xml

# Expose the default Tomcat port
EXPOSE 9080

USER root

# Set the command to run Tomcat
CMD ["/opt/apache-tomcat-9.0.78/bin/catalina.sh", "run"]
