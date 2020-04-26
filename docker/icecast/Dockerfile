# Copied from here: https://github.com/vimagick/dockerfiles/blob/master/icecast/Dockerfile
FROM alpine

RUN apk add --no-cache icecast

EXPOSE 8000

CMD ["icecast", "-c", "/etc/icecast.xml"]

