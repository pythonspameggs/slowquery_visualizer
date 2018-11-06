FROM alpine AS builder

# percona-toolkit
ENV PERCONA_TOOLKIT_VERSION 3.0.12
RUN apk add --no-cache perl perl-dbi perl-dbd-mysql perl-io-socket-ssl perl-term-readkey gcc g++ libc-dev make wget \
    && wget -O /tmp/percona-toolkit.tar.gz https://www.percona.com/downloads/percona-toolkit/${PERCONA_TOOLKIT_VERSION}/source/tarball/percona-toolkit-${PERCONA_TOOLKIT_VERSION}.tar.gz \
    && tar -xzvf /tmp/percona-toolkit.tar.gz -C /tmp \
    && cd /tmp/percona-toolkit-${PERCONA_TOOLKIT_VERSION} \
    && perl Makefile.PL \
    && make \
    && make test \
    && make install

FROM fluent/fluentd:latest

# Fluentd plugin
RUN buildDeps="ruby-dev gcc g++ libc-dev make wget" \
    && apk add --no-cache $buildDeps \
    && echo "http://dl-4.alpinelinux.org/alpine/edge/main/" >> /etc/apk/repositories \
    && apk add --no-cache mariadb-connector-c-dev perl \
    && gem install mysql2 \
    && gem install fluent-plugin-nata2 \
    && gem install fluent-plugin-mysql_explain \
    && gem install fluent-plugin-sql_fingerprint \
    && gem install fluent-plugin-slack \
    && gem install fluent-plugin-elasticsearch \
    && rm -rf /tmp/* /var/tmp/* /usr/lib/ruby/gems/*/cache/*.gem \
    && apk del $buildDeps

COPY --from=builder /usr/local/bin/pt-fingerprint /usr/bin/pt-fingerprint
