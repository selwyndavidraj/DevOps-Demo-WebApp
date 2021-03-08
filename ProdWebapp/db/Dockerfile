#
# example Dockerfile for https://docs.docker.com/engine/examples/postgresql_service/
#

FROM ubuntu:16.04

# Add the PostgreSQL PGP key to verify their Debian packages.
# It should be the same key as https://www.postgresql.org/media/keys/ACCC4CF8.asc
RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8

# Add PostgreSQL's repository. It contains the most recent stable release
#     of PostgreSQL, ``9.3``.
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list

# Install ``python-software-properties``, ``software-properties-common`` and PostgreSQL 9.3
#  There are some warnings (in red) that show up during the build. You can hide
#  them by prefixing each apt-get statement with DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y python-software-properties software-properties-common postgresql-9.3 postgresql-client-9.3 postgresql-contrib-9.3

# Note: The official Debian and Ubuntu images automatically ``apt-get clean``
# after each ``apt-get``

#RUN apt-get install python3 pip
# RUN pip3 install -r  psycopg2

# Run the rest of the commands as the ``postgres`` user created by the ``postgres-9.3`` package when it was ``apt-get installed``
USER postgres

# Create a PostgreSQL role named ``docker`` with ``docker`` as the password and
# then create a database `docker` owned by the ``docker`` role.
# Note: here we use ``&&\`` to run commands one after the other - the ``\``
#       allows the RUN command to span multiple lines.
RUN    /etc/init.d/postgresql start &&\
    psql --command "CREATE USER docker WITH SUPERUSER PASSWORD 'docker';" &&\
    createdb -O docker docker

# Adjust PostgreSQL configuration so that remote connections to the
# database are possible.
RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/9.3/main/pg_hba.conf

# And add ``listen_addresses`` to ``/etc/postgresql/9.3/main/postgresql.conf``
RUN echo "listen_addresses='*'" >> /etc/postgresql/9.3/main/postgresql.conf

# Expose the PostgreSQL port
EXPOSE 5432


# Add VOLUMEs to allow backup of config, logs and databases
VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql"]


RUN /etc/init.d/postgresql start &&\
    psql --command "\connect docker;" &&\
    psql --dbname=docker --command "ALTER USER postgres WITH PASSWORD 'password';" &&\
    psql --dbname=docker --command "CREATE SEQUENCE public.ticket_sequence INCREMENT 1 START 1000;" &&\
    psql --dbname=docker --command "CREATE TABLE public.avt(tnum bigint default nextval('ticket_sequence'::regclass));" &&\
    psql --dbname=docker --command "\dt" &&\
    psql --dbname=docker --command "ALTER TABLE avt ADD COLUMN product varchar(100);" &&\
    psql --dbname=docker --command "ALTER TABLE avt ADD COLUMN pdesc varchar(1000);" &&\
    psql --dbname=docker --command "ALTER TABLE avt ADD COLUMN cont varchar(10);" &&\
    psql --dbname=docker --command "ALTER TABLE avt ADD COLUMN datee varchar(100);" &&\
    psql --dbname=docker --command "ALTER TABLE avt ADD COLUMN statuss varchar(100);" &&\
    psql --dbname=docker --command "INSERT INTO public.avt(product, pdesc, cont, datee, statuss ) VALUES ('DIGITALTV', 'NW Issue','9884970793','15/03/2017','OPEN' ) RETURNING *;" &&\
    psql --dbname=docker --command "INSERT INTO public.avt(pdesc) VALUES ('NOT WORKING') RETURNING *;" &&\
    psql --dbname=docker --command "INSERT INTO public.avt(cont) VALUES ('9884570793') RETURNING *;" &&\
    psql --dbname=docker --command "INSERT INTO public.avt(datee) VALUES ('20/03/2017') RETURNING *;" &&\
    psql --dbname=docker --command "INSERT INTO public.avt(statuss) VALUES ('NEW') RETURNING *;" &&\
    psql --dbname=docker --command "SELECT * FROM avt;"



# Set the default command to run when starting the container
CMD ["/usr/lib/postgresql/9.3/bin/postgres", "-D", "/var/lib/postgresql/9.3/main", "-c", "config_file=/etc/postgresql/9.3/main/postgresql.conf"]
