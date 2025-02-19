# base image
FROM nginx

ENV base_path=/usr/share/nginx/html

ENV i18n_path=${base_path}/assets/i18n

ENV entity_spec_path=${base_path}/assets/entity-spec

# can be passed during Docker build as build time environment for github branch to pickup configuration from.
ARG container_user=mosip

# can be passed during Docker build as build time environment for github branch to pickup configuration from.
ARG container_user_group=mosip

# can be passed during Docker build as build time environment for github branch to pickup configuration from.
ARG container_user_uid=1001

# can be passed during Docker build as build time environment for github branch to pickup configuration from.
ARG container_user_gid=1001

# install packages and create user
RUN apt-get -y update \
&& apt-get install -y unzip wget \
&& groupadd -g ${container_user_gid} ${container_user_group} \
&& useradd -u ${container_user_uid} -g ${container_user_group} -s /bin/sh -m ${container_user} \
&& mkdir -p /var/run/nginx /var/tmp/nginx \
&& chown -R ${container_user}:${container_user} /usr/share/nginx /var/run/nginx /var/tmp/nginx

# set working directory for the user
WORKDIR /home/${container_user}

ADD ./nginx.conf /etc/nginx/nginx.conf

ADD default.conf /etc/nginx/conf.d/

ADD dist ${base_path}

# can be passed during Docker build as build time environment for artifactory URL
ARG artifactory_url
# environment variable to pass artifactory url, at docker runtime
ENV artifactory_url_env=${artifactory_url}

# install the needed packages including wget and a unzip library 
#RUN apt-get update -y && apt-get install -y wget && apt-get install -y unzip

# change permissions of file inside working dir
RUN chown -R ${container_user}:${container_user} ${base_path}/assets/i18n
RUN chown -R ${container_user}:${container_user} ${base_path}/assets/entity-spec

# select container user for all tasks
USER ${container_user_uid}:${container_user_gid}

EXPOSE 8080

#get the pmp i18n bundle zip from artifactory
CMD wget -q --show-progress "${artifactory_url_env}"/artifactory/libs-release-local/i18n/pmp-i18n-bundle.zip -O "${i18n_path}"/pmp-i18n-bundle.zip ; \
    wget -q --show-progress "${artifactory_url_env}"/artifactory/libs-release-local/i18n/pmp-entity-spec-bundle.zip -O "${entity_spec_path}"/pmp-entity-spec-bundle.zip ; \
    cd ${entity_spec_path} ; \
    unzip -o pmp-entity-spec-bundle.zip ; \
    cd ${i18n_path} ; \
    unzip -o pmp-i18n-bundle.zip ; \
    nginx ; \
    sleep infinity
	
