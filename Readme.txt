##################################################
# srs on Docker for Raspberry Pi                 #
#              REF: https://github.com/ossrs/srs # 
##################################################


###############################################################################
# Docker build
time docker build --no-cache -t ernestgwilsonii/docker-raspberry-pi-srs:2.0.263 -f Dockerfile.armhf .
docker images

# Verify 
docker run -it -p 1935:1935 -p 1985:198585 -p 8080:8080 ernestgwilsonii/docker-raspberry-pi-srs:2.0.263
# From another ssh session:
#docker ps

# Upload to Docker Hub
docker login
docker push ernestgwilsonii/docker-raspberry-pi-srs:2.0.263
###############################################################################


###############################################################################
# First time setup #
####################
# Create bind mounted directies
sudo mkdir -p /opt/srs
sudo chmod -R a+rw /opt/srs

##########
# Deploy #
##########
# Deploy the stack into a Docker Swarm
docker stack deploy -c docker-compose.yml srs
# docker stack rm srs

# Verify
docker service ls | grep srs
docker service logs -f srs_srs


##################
# srs Basic Test #
##################
# REF: https://github.com/ossrs/srs

# Start a live stream from a Raspberry Pi USB camera to the srs server
ffmpeg -i /dev/video0 -vcodec libx264 -preset ultrafast -tune zerolatency -r 10 -async 1 -maxrate 750k -bufsize 3000k -f flv rtmp://192.168.1.12/live/livestream

# Open VLC and connect to the srs and view the rtmp video stream
rtmp://192.168.1.12:1935/live/livestream

###############################################################################

