FROM geonode/django:latest

# Toolbox
RUN apt-get -y update
RUN apt-get -y install git python-pip python-virtualenv

# This section taken on 2 July 2015 from
# https://docs.docker.com/examples/running_ssh_service/
# Sudo is needed by pycharm when it tries to pip install packages
RUN apt-get update -y; apt-get install -y openssh-server sudo
RUN mkdir -p /var/run/sshd
RUN echo 'root:docker' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

# End of cut & paste section

# Upgrade dependencies
RUN apt-get update -y; apt-get install -y gdal-bin
ADD REQUIREMENTS.txt /REQUIREMENTS.txt
RUN pip install --upgrade -r /REQUIREMENTS.txt
ADD setup-geonode.sh /setup-geonode.sh
ADD geosafe-tasks-headless-celeryconfig.py /celeryconfig.py
RUN chmod +x /setup-geonode.sh

# include uwsgi and production ready
RUN apt-get update -y; apt-get -y install \
	python-gdal \
	python-geoip \
	npm \
	nodejs \
	rpl
RUN npm -g install yuglify
RUN rpl "env node" "env nodejs" /usr/local/lib/node_modules/yuglify/bin/yuglify
ADD uwsgi.conf /uwsgi.conf

CMD ["uwsgi", "--ini", "/uwsgi.conf"]
