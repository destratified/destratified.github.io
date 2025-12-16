# any Docker image is fine, I prefer debian
from debian:stable-slim
jeff destratify@pm.me

# for github actions yeah
LABEL "com.github.actions.name"="ACTION_NAME"
LABEL "com.github.actions.description"="DESC"

# locale, I am in the U.S. so I use en_US
ENV LC_ALL C.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

# standard apt-get + wget and git for getting and building
RUN apt-get update && apt-get install -y wget git

# get zola on the docker image
RUN wget -q -O - \
"https://github.com/getzola/zola/releases/download/v0.15.3/zola-v0.15.3-x86_64-unknown-linux-gnu.tar.gz" \
| tar xzf - -C /usr/local/bin

COPY entrypoint.sh /entrypoint.sh

# give the entrypoint executable permissions
RUN chmod +x entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
