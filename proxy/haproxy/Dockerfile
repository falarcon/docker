FROM ubuntu:12.04
MAINTAINER Alan Boudreault "boudreault.alan@gmail.com"

##### CONFIG #####
ENV HAPROXY_VERSION 1.5-dev22
ENV HAPROXY haproxy-$HAPROXY_VERSION
ENV TMP_DIR /tmp
ENV SSL_SUBJ /C=CA/ST=QC/L=Saguenay/O=Dis/CN=alanb.ca
##### END CONFIG #####

# Compile and Install haproxy 1.5
RUN apt-get update
RUN apt-get install -y wget build-essential libssl-dev openssl
RUN wget -O $TMP_DIR/$HAPROXY.tar.gz http://haproxy.1wt.eu/download/1.5/src/devel/$HAPROXY.tar.gz
RUN cd  $TMP_DIR ; tar -xzf $HAPROXY.tar.gz
RUN make -C $TMP_DIR/$HAPROXY TARGET=generic USE_OPENSSL=1
RUN make -C $TMP_DIR/$HAPROXY install	     

# Create haproxy user
RUN addgroup --system haproxy
RUN adduser --system --no-create-home --ingroup haproxy haproxy

# Generate SSL certificate for https
RUN openssl req -new -newkey rsa:2048 -days 1825 -nodes -x509 -subj $SSL_SUBJ -keyout server.key -out server.crt
RUN cat server.crt server.key > server.pem
RUN mv server.* /etc/ssl/certs/
RUN chmod o-r /etc/ssl/certs/*
RUN chown haproxy.haproxy /etc/ssl/certs/server.pem

ADD ./haproxy.conf /etc/haproxy/haproxy.conf

EXPOSE 80
EXPOSE 443

CMD /usr/local/sbin/haproxy -f /etc/haproxy/haproxy.conf
