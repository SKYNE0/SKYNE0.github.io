

```
version: '3.7'

# Settings and configurations that are common for all containers
x-minio-common: &minio-common
  image: harbor.tarsocial.com/base-images/minio:20220809
  command: server --console-address ":9001" http://minio{1...4}/data{1...2}
  expose:
    - "9000"
    - "9001"
  environment:
    MINIO_ROOT_USER: admin
    MINIO_ROOT_PASSWORD: minioadmin
  healthcheck:
    test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
    interval: 30s
    timeout: 20s
    retries: 3

# starts 4 docker containers running minio server instances.
# using nginx reverse proxy, load balancing, you can access
# it through port 9000.
services:
  minio1:
    <<: *minio-common
    hostname: minio1
    volumes:
      - data1:/data1
      - data2:/data2

  minio2:
    <<: *minio-common
    hostname: minio2
    volumes:
      - data3:/data1
      - data4:/data2

  minio3:
    <<: *minio-common
    hostname: minio3
    volumes:
      - data5:/data1
      - data6:/data2

  minio4:
    <<: *minio-common
    hostname: minio4
    volumes:
      - data7:/data1
      - data8:/data2

  nginx:
    image: nginx:1.19.2-alpine
    hostname: nginx
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    ports:
      - "9000:9000"
      - "9001:9001"
    depends_on:
      - minio1
      - minio2
      - minio3
      - minio4

## By default this config uses default local driver,
## For custom volumes replace with volume driver configuration.
volumes:
  data1: 
    driver_opts:
      type: ext4
      o: bind
      device: /mnt01/minio
  data2: 
    driver_opts:
      type: ext4
      o: bind
      device: /mnt02/minio
  data3: 
    driver_opts:
      type: ext4
      o: bind
      device: /mnt03/minio
  data4: 
    driver_opts:
      type: ext4
      o: bind
      device: /mnt04/minio
  data5: 
    driver_opts:
      type: ext4
      o: bind
      device: /mnt05/minio
  data6: 
    driver_opts:
      type: ext4
      o: bind
      device: /mnt06/minio
  data7: 
    driver_opts:
      type: ext4
      o: bind
      device: /mnt07/minio
  data8: 
    driver_opts:
      type: ext4
      o: bind
      device: /mnt08/minio
```