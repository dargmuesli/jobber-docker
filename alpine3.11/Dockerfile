FROM golang:1.11 AS s1

ENV JOBBER_VER v1.4.5

WORKDIR /go_wkspc/src/github.com/dshearer
COPY jobber-maint-1.4 jobber
WORKDIR /go_wkspc/src/github.com/dshearer/jobber
RUN make check && \
    make install DESTDIR=/jobber-dist/

FROM alpine:3.15.3

# make user
ENV USERID 1000
RUN addgroup jobberuser && \
    adduser -S -u "${USERID}" -G jobberuser jobberuser && \
    mkdir -p "/var/jobber/${USERID}" && \
    chown -R jobberuser:jobberuser "/var/jobber/${USERID}"

# install jobber
RUN apk add --no-cache libc6-compat
RUN mkdir /jobber
COPY --from=0 /jobber-dist/etc/jobber.conf /etc/jobber.conf
COPY --from=0 /jobber-dist/usr/local/libexec/jobberrunner /jobber/jobberrunner
COPY --from=0 /jobber-dist/usr/local/bin/jobber /jobber/jobber
ENV PATH /jobber:${PATH}

# add jobfile
COPY --chown=jobberuser:jobberuser jobfile /home/jobberuser/.jobber
RUN chmod 0600 /home/jobberuser/.jobber

USER jobberuser
CMD ["/jobber/jobberrunner", "-u", "/var/jobber/1000/cmd.sock", "/home/jobberuser/.jobber"]
