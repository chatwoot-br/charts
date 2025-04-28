ARG PGVECTOR_TAG=v0.8.0
ARG PGSQL_HTTP_TAG=v1.6.3
ARG PG_MAJOR=17.4.0
FROM bitnami/git:2 AS git

ARG PGVECTOR_TAG
ARG PGSQL_HTTP_TAG
WORKDIR /workspace
RUN git clone https://github.com/pgvector/pgvector && cd pgvector && git checkout ${PGVECTOR_TAG}
RUN git clone https://github.com/pramsey/pgsql-http && cd pgsql-http && git checkout ${PGSQL_HTTP_TAG}

ARG PG_MAJOR
FROM bitnami/postgresql:${PG_MAJOR}

USER root
COPY --from=git /workspace/pgvector /tmp/pgvector
COPY --from=git /workspace/pgsql-http /tmp/pgsql-http

# Update package lists
RUN apt-get update && \
    apt-mark hold locales

# Install build dependencies
RUN apt-get install -y --no-install-recommends \
    build-essential \
    libcurl4-openssl-dev

# Build and install pgvector
RUN cd /tmp/pgvector && \
    make clean && \
    make OPTFLAGS="" && \
    make install && \
    mkdir -p /usr/share/doc/pgvector && \
    cp LICENSE README.md /usr/share/doc/pgvector

# Build and install pgsql-http
RUN cd /tmp/pgsql-http && \
    make && \
    make install && \
    mkdir -p /usr/share/doc/pgsql-http && \
    cp README.md /usr/share/doc/pgsql-http && \
    echo "pgsql-http is licensed under MIT License" > /usr/share/doc/pgsql-http/LICENSE_INFO

# Cleanup
RUN rm -r /tmp/pgvector /tmp/pgsql-http && \
    apt-get remove -y build-essential && \
    apt-get autoremove -y && \
    apt-mark unhold locales && \
    rm -rf /var/lib/apt/lists/*