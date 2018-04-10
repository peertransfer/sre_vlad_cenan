# Installing sinatra

## High-Level Instructions

* Update apt-get
* Install Ruby
* Install nginx
* Configure nginx
* Restart nginx
* Clone the repo
* Install Bundler
* Install project dependencies
* Install thin service
* Create a new thin config for the blog and copy into /etc/thin
* Start / Re-start the thin service

## Installation Commands for Debian Jessie

```
sudo bash -lc '

# Update apt-get

apt-get update

# Build Ruby

apt-get install build-essential libssl-dev libyaml-dev libreadline-dev openssl curl git zlib1g-dev bison libxml2-dev libxslt1-dev libcurl4-openssl-dev nodejs libsqlite3-dev sqlite3 --yes

mkdir ~/ruby
cd ~/ruby
wget https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.bz2
tar -xjf ruby-2.3.1.tar.bz2
cd ruby-2.3.1
./configure --disable-install-doc
make install
rm -rf ~/ruby
cd

# Install nginx

apt-get install nginx --yes

# Configure apache

cat > /etc/nginx/sites-available/blog.conf <<EOF
server {
  listen 80;
  server_name       localhost default_server;

  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
    proxy_set_header Host \$http_host;
  }
}
EOF

rm -f /etc/nginx/sites-enabled/default
ln -s /etc/nginx/sites-available/blog.conf /etc/nginx/sites-enabled/

# Restart nginx

service nginx restart

# Install Bundler

gem install bundler

# Create an user

useradd --system --create-home --shell /usr/sbin/nologin blog
'

# Change to the user and deploy application
sudo -u blog bash -lc '
cd $HOME

# Clone the repo

git clone https://github.com/peertransfer/sinatra-skeleton sinatra-skeleton

cd sinatra-skeleton

# Install project dependencies

bundle install --path vendor/bundle -j 10
bundle binstubs thin
'

sudo bash -lc '

# Create a new thin config for the blog and copy into /etc/thin
mkdir /etc/thin

cat > /etc/thin/blog.yml <<EOF
pid: tmp/pids/thin.pid
log: log/thin.log
timeout: 30
max_conns: 1024
port: 3000
max_persistent_conns: 512
chdir: /home/blog/sinatra-skeleton
environment: development
servers: 1
address: 0.0.0.0
daemonize: true
EOF

# Install thin service
cat > /etc/init.d/thin <<EOF
# /etc/init.d/thin

#!/bin/sh
### BEGIN INIT INFO
# Provides:          thin
# Required-Start:    $local_fs $remote_fs
# Required-Stop:     $local_fs $remote_fs
# Default-Start:     2 3 4 5
# Default-Stop:      S 0 1 6
# Short-Description: thin initscript
# Description:       thin
### END INIT INFO

# Original author: Forrest Robertson

# Do NOT "set -e"

DAEMON=/home/blog/sinatra-skeleton/bin/thin
SCRIPT_NAME=/etc/init.d/thin
CONFIG_PATH=/etc/thin
HOME=/home/blog

# Exit if the package is not installed
[ -x "\$DAEMON" ] || exit 0

case "\$1" in
  start)
  HOME=\$HOME \$DAEMON start --all \$CONFIG_PATH
  ;;
  stop)
  HOME=\$HOME \$DAEMON stop --all \$CONFIG_PATH
  ;;
  restart)
  HOME=\$HOME \$DAEMON restart --all \$CONFIG_PATH
  ;;
  *)
  echo "Usage: \$SCRIPT_NAME {start|stop|restart}" >&2
  exit 3
  ;;
esac

:
EOF

chmod +x /etc/init.d/thin
/usr/sbin/update-rc.d -f thin defaults

# Start / Re-start the thin service

/etc/init.d/thin start
'
```
