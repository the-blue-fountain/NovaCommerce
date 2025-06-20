Spring Boot Microservices Tutorial : https://programmingtechie.com/articles

## Flyway & Liquibase

- We are using flyway for migrations and version control, so we set the option `spring.jpa.hibernate.ddl-auto = none` in the application.properties file. Doing so disables the ddl handling option, means now Hibernate does not automatically make migrations or delete data from the database.
- flyway helps to version control our database schema, so we can easily rollback to a previous version of the database in case of any problems. Hibernate does not allow rollback. Liquibase is another database migration tool. 
- We write SQL scripts for databse migration using Flyway. The migration scripts will be stored in the `src/main/resources/db/migration` folder, where flyway looks for such scripts. 
- We have to folllow a specific naming convention while writing migration scripts for flyway. The scripts should be named as `V<version number>__<name>.sql`. You can see example scripts in the application itself. 
- flyway maintains the current migration state. So the migration script will not be run each time the application starts. It will only run when the migration is required.

## Docker 

- The docker compose file creates the database using the script mysql/init.sql, whose location is given in the `docker-compose.yml` file.

## Spring Cloud OpenFeign(not used really)

- We are using it to establish synchronous communication.
- Just below the `@SpringBootApplication` annotation, we have the `@EnableFeignClients` annotation. This annotation will auto-configure the necessary classes to use OpenFeign in our project(like order-service). 

## API Gateway

- it acts as an entry point for the requests
- forward the requests to the downstream microservices
- it is commonly used in the distributed systems and microservices architecture