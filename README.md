# Usage

## Create

```
git clone https://github.com/bgpat/isucon5f-docker
cd isucon5f-docker
docker-compose up -d
```

## Bench

```
docker-compose exec bench bash -c 'TERM=xterm-256color sudo -uisucon /home/isucon/bench.sh image1'
```
