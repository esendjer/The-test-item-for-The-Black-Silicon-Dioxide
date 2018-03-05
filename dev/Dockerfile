FROM "postgres:9.4"
# FROM postgres:9.4                   # for dockre.io

RUN apt-get update \
	&& apt-get install -y curl \
	&& curl -s http://packages.2ndquadrant.com/bdr/apt/AA7A6805.asc | apt-key add - \
	&& echo 'deb http://packages.2ndquadrant.com/bdr/apt/ jessie-2ndquadrant main ' > /etc/apt/sources.list.d/2ndquadrant.list \
	&& apt-get update \
	&& apt-get install -y postgresql-bdr-$PG_MAJOR-bdr-plugin
