Running environment: Ubuntu 16.04 server LTS

Need to install nginx manually with the rtmp module

Additional tools:
-ffmpeg3

$ sudo add-apt-repository ppa:jonathonf/ffmpeg-3
$ sudo apt update
$ sudo apt install ffmpeg libav-tools x264 x265

-pcre
-openssl

To simulate the client traffic, 
we use
-docker
-docker-compose (version 1.20.0 or above, support variable substitution)
-vlc (version 2.2.7 or higher) ( Need some modification in order to run as root)

build image with vlc installed.
Use docker-compose the scale the number of instance
and run vlc-player without display (cvlc -Vdummy xxx.m3u8)

(Vlc player is tested to be able to adapt HLS bitrate automatically)

To monitor traffic, server can install:
- wondershaper (clone from git and sudo make install)
- nethogs 
- etherape

To test for http request timing
$ curl -s -w "%{time_total}\n" -o /dev/null http://172.18.3.225

To test the traffic in nginx, use "ngxtop"
$ pip install ngxtop
$ ngxtop -l /var/log/nginx/access.log

To run the vlc_gui demo.py, need to install pyQt4 (also qt-creator if need to edit)
$ sudo apt-get install python-qt4 python-qt4-* qt4-designer python-pyqt5 python-pyqt5.qtmultimedia python3-pyqt5

Using Pyqt.Phonon, need to install gStreamer and its plugin
$sudo apt-get install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-doc gstreamer1.0-tools
$sudo apt-get install python3-pyqt4.phonon

Install mpv player and libmpv.so
$sudo apt-get install mpv libmpv-dev

Use goaccess to analyze and plot statistic of nginx server access log
Installation: https://blog.gtwang.org/linux/analysing-nginx-logs-using-goaccess/

Command line monitoring
$ goaccess -f /var/log/nginx/access.log

HTML monitoring
$ goaccess -f /var/log/nginx/access.log -o /home/hmcheng/nginx/html/report.html --log-format=COMBINED --real-time-html --html-prefs='{"theme":"bright","perPage":10}' 

within 1 hour
$ sed -n '/'$(date '+%d\/%b\/%Y' -d '1 hour ago')'/,$ p' /var/log/nginx/access.log | goaccess -a -o /home/hmcheng/nginx/html/report.html --log-format=COMBINED --real-time-html --html-prefs='{"theme":"bright","perPage":10}' 

and go to localhost/report.html


Install nginx amplify
Additional nginx metrix

log_format  main_ext  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for" '
                      '"$host" sn="$server_name" '
                      'rt=$request_time '
                      'ua="$upstream_addr" us="$upstream_status" '
                      'ut="$upstream_response_time" ul="$upstream_response_length" '
                      'cs=$upstream_cache_status' ;
                      
access_log  /var/log/nginx/access.log  main_ext;
error_log  /var/log/nginx/error.log warn;
