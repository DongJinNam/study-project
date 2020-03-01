# start from base
FROM ubuntu:latest

LABEL maintainer="DongJin Nam <ehdwls6856@gmail.com>"

# install system-wide deps for python and node
RUN apt-get update
RUN apt-get -y install python-pip python-dev curl gnupg
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt-get -y install nodejs

# copy our application code
ADD flask-app /opt/flask-app
WORKDIR /opt/flask-app

# fetch app specific deps
RUN npm install
RUN npm run build
RUN pip install -r requirements.txt

# expose port
EXPOSE 5000

# start app
CMD [ "python", "./app.py" ]
