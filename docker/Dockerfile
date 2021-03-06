# stage 1, install deps
FROM python:3.8-alpine AS builder
WORKDIR /scout
RUN python -m venv .venv && .venv/bin/pip install --no-cache-dir -U pip setuptools
RUN \
  apk update && \
  apk upgrade && \
  apk add build-base alpine-sdk g++ gcc make openssl-dev musl-dev libffi-dev 
COPY requirements.txt /tmp/requirements.txt
RUN \
  .venv/bin/pip install --no-cache-dir -r /tmp/requirements.txt && \
  find /scout/.venv \( -type d -a -name test -o -name tests \) -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) -delete
  
# stage 2, only necessary files
FROM python:3.8-alpine
WORKDIR /scout
COPY ./bin/setup_aws.sh /tmp
COPY --from=builder /scout /scout
RUN \
  apk update --no-cache && \
  apk upgrade --no-cache && \
  /tmp/setup_aws.sh && \
  rm -vf /tmp/setup_aws.sh
ENV PATH="/scout/.venv/bin:$PATH"
ENV AWS_CONFIG_FILE="/scout/.aws/config"
CMD ["/bin/sh"]
