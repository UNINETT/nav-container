FROM python:2.7
LABEL description="A carbon-cache instance configured explicitly for use with NAV"
LABEL maintainer="Morten Brekkevold <morten.brekkevold@uninett.no>"

# Add Tini
ENV TINI_VERSION v0.14.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
ENTRYPOINT ["/tini", "--"]

ENV LIBDIR /usr/local/lib/python2.7/site-packages
ENV GRAPHITE_CONF_DIR /var/lib/graphite/conf
ENV GRAPHITE_STORAGE_DIR /var/lib/graphite/storage

# Add user to run it, and install the software
RUN adduser --system --group --no-create-home --home=/var/lib/graphite --shell=/bin/bash graphite
RUN pip install whisper
RUN pip install carbon --install-option="--install-scripts=/usr/local/bin" --install-option="--install-lib=$LIBDIR" --install-option="--install-data=/var/lib/graphite"

# Basic configuration and permissions
COPY carbon.conf ${GRAPHITE_CONF_DIR}/carbon.conf
COPY --from=mbrekkevold/nav:latest /etc/nav/graphite/storage-schemas.conf ${GRAPHITE_CONF_DIR}/storage-schemas.conf
COPY --from=mbrekkevold/nav:latest /etc/nav/graphite/storage-aggregation.conf ${GRAPHITE_CONF_DIR}/storage-aggregation.conf

RUN chown -R graphite:graphite /var/lib/graphite

EXPOSE 2003/udp 2003 2004 7002
VOLUME ["/var/lib/graphite/storage/whisper"]
USER   graphite

CMD    ["/usr/local/bin/carbon-cache.py", "--nodaemon", "--config=/var/lib/graphite/conf/carbon.conf", "start"]
