#!/bin/bash
#
# Stunredis.sh
#
# Copyright 2018 IBM Corp.
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#    Unless required by applicable law or agreed to in writing, software
#    distributed under the License is distributed on an "AS IS" BASIS,
#    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#    See the License for the specific language governing permissions and
#    limitations under the License.

DATABASE_URL=$1

if [ -z "$1" ]; then
  echo "stunredis rediss://redis.example.com:6379 [localbindport]"
  exit 1
fi

# This is the location of the validation chain file
cabundle=/etc/pki/tls/certs/ca-bundle.crt

# URL parsing based on https://stackoverflow.com/a/17287984
# extract the protocol
proto="`echo "$DATABASE_URL" | grep '://' | sed -e's,^\(.*://\).*,\1,g'`"
# remove the protocol
url=`echo "$DATABASE_URL" | sed -e s,$proto,,g`
# extract the user and password (if any)
userpass="`echo "$url" | grep @ | cut -d@ -f1`"
pass=`echo "$userpass" | grep : | cut -d: -f2-`
if [ -n "$pass" ]; then
    user=`echo "$userpass" | grep : | cut -d: -f1`
else
    user="$userpass"
fi
hostport=${url#"$userpass@"}
port=`echo "$hostport" | grep : | cut -d: -f2`
if [ -n "$port" ]; then
    host=`echo "$hostport" | grep : | cut -d: -f1`
else
    host="$hostport"
fi

# Now we create our configuration file as a variable
acceptsock=$"${HOME}/.redis.${BASHPID}.sock"
stunnelconf=""
stunnelconf+=$"foreground=yes\n"
stunnelconf+=$"pid=\n"
stunnelconf+=$"[redis-cli]\n"
stunnelconf+=$"client=yes\n"
stunnelconf+=$"accept=$acceptsock\n"
stunnelconf+=$"CAfile=$cabundle\n"
stunnelconf+=$"verify=2\n"
stunnelconf+=$"connect=$hostport\n"

# We expand that out in echo and feed the result to stunnel
# which is set to take its configuration from a file descriptor
# in this case, 0, stdin.

echo -e $stunnelconf | stunnel -fd 0 &

# Grab the pid
stunnelpid=$!
# Sleep a moment to let stunnel start
sleep 1
# Assuming it's running...
if kill -0 $stunnelpid &>/dev/null; then
  # Now call redis-cli for the user to interact with
  if [[ -n "${pass}" ]]; then
    redis-cli -s "$acceptsock" -a "${pass}"
  else
    redis-cli -s "$acceptsock"
  fi
  # Once they leave that, kill the stunnel
  kill $stunnelpid &>/dev/null
  wait $stunnelpid
  exit 0
fi
echo "stunnel faild to start" 1>&2
exit 1
