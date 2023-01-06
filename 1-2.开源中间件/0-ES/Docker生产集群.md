

```
FROM elasticsearch:7.3.0

COPY ./elasticsearch/config /usr/share/elasticsearch/config

RUN groupadd -g 2000 es &&     adduser -u 2000 -g 2000 -G 0 -d /usr/share/elasticsearch es &&     chmod 0775 /usr/share/elasticsearch &&  chgrp 0 /usr/share/elasticsearch

RUN mkdir -p /mnt/es_backup && chown -R es:es /usr/share/elasticsearch /mnt  /usr/local/bin/docker-entrypoint.sh

RUN sed -i 's#1000#2000#g' /usr/local/bin/docker-entrypoint.sh
```