# Stage 1

FROM node:18-alpine3.14 as build-step

# Install ssh client and git
RUN apk add --no-cache git openssh

# Set Time Zone GMT-3 (São Paulo)
RUN apk add tzdata
RUN cp /usr/share/zoneinfo/Brazil/East /etc/localtime

# add credentials on build
RUN mkdir /root/.ssh/ && chmod 700 /root/.ssh/ && ssh-keyscan bitbucket.org > /root/.ssh/known_hosts
ADD clientKey /root/.ssh/clientKey
ADD clientKey.pub /root/.ssh/clientKey.pub
RUN eval `ssh-agent -s`

# SSH Permisssions
RUN chmod 600 /root/.ssh/clientKey && chmod 600 /root/.ssh/clientKey.pub
RUN  echo "IdentityFile ~/.ssh/clientKey" >> /etc/ssh/ssh_config
RUN cat /root/.ssh/clientKey >> /root/.ssh/known_hosts

# Create app directory
WORKDIR /app

# Copy app directory
RUN git clone git@bitbucket.org:me/app-client.git

# Changing app directory
WORKDIR /app/app-client

# Install app dependencies
RUN npm install -g @angular/cli
RUN npm install

# Run Build
RUN ng build

# Stage 2

FROM nginx:1.20.2-alpine

# Clean nginx
RUN rm -rf /usr/share/nginx/html/*

# Copy dist
COPY --from=build-step /app/app-client/ /usr/share/nginx/html/

COPY nginx.conf /etc/nginx/nginx.conf

WORKDIR /usr/share/nginx/html

# Permission
RUN chown root /usr/share/nginx/html/*
RUN chmod 755 /usr/share/nginx/html/*

# Expose port
EXPOSE 4200

# Start
CMD ["nginx", "-g", "daemon off;"]
