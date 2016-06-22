#!/bin/bash

die() {
  log $@
  exit 1
}

log() {
  echo "[`date +%Y%m%d-%H:%M:%S`] $@" >&2
}

print_usage() {
  log "usage: $0 <start|stop|restart> <app name>"
}

dir=`dirname $0`
dir=`cd $dir/..; pwd`

if [ $# -ne 2 ]; then
  print_usage
  exit 1
fi

REGISTRY=registry.docker.dev.sogou-inc.com:5000
LOCAL_LOG_ROOT_DIR=$dir/logs
LOCAL_DATA_ROOT_DIR=$dir/data

op=$1
app=$2
image=`awk -F"," '{if($1=="'$app'") {print $2}}' conf/app.conf`
version=`awk -F"," '{if($1=="'$app'") {print $3}}' conf/app.conf`
app_dir=/search/$app
log_dir=/search/$app/logs
data_dir=/search/$app/data
local_log_dir=$LOCAL_LOG_ROOT_DIR/$app; mkdir -p $local_log_dir
local_data_dir=$LOCAL_DATA_ROOT_DIR/$app; mkdir -p $local_data_dir

start() {
  docker pull $REGISTRY/$image:$version
  docker run -d --net=host \
    --name $app \
    -v /etc/localtime:/etc/localtime \
    -v $local_log_dir:$log_dir \
    -v $local_data_dir:$data_dir \
    $REGISTRY/$image:$version
}

stop() {
  docker stop $app
  docker rm $app
}

restart() {
  stop
  if [ $? -ne 0 ]; then
    die "fail to stop $app"
  fi
  start
}

case $op in
  start)
    start
    exit $?
    ;;
  stop)
    stop
    exit $?
    ;;
  retart)
    restart
    exit $?
    ;;
  *)
    print_usage
    exit 1
    ;;
esac
