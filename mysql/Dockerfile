FROM sashiaeec/flyway:latest
WORKDIR /opt/flyway 
COPY flyway.conf conf/
COPY sql sql
CMD ["./wait-for-it.sh", "tsdr-db:3306", "--", "./flyway", "-password=${tsdr_db_password}", "migrate"]
