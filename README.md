
Standardni PostgreSQL/NODE.JS

services:
  ma20240469-backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: ma20240469-backend
    ports:
      - "5000:5000"
    volumes:
      - /app/node_modules
      - ./backend:/app
    env_file:
      - ./env/backend.env
    depends_on:
      ma20240469-db:
        condition: service_healthy
    networks:
      - ma20240469-network

  ma20240469-frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: ma20240469-frontend
    ports:
      - "7000:7000"
    volumes:
      - /app/node_modules
      - ./frontend:/app
    env_file:
      - ./env/frontend.env
    depends_on:
      - ma20240469-backend
    networks:
      - ma20240469-network

  ma20240469-db:
    image: postgres:16-alpine
    container_name: ma20240469-db
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql:ro
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U student -d reservations"]
      interval: 10s
      timeout: 10s
      retries: 6
    networks:
      - ma20240469-network

networks:
  ma20240469-network:
    name: ma20240469-network
    driver: bridge

volumes:
  postgres-data:





-----------------------------------------------
MySQL 8.0 & Python setup/PYTHON

name: ma20240469-app

services:
  ma20240469-back:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: ma20240469-back
    ports:
      - "5000:5000"
    volumes:
      - /app/__pycache__
      - ./backend:/app
    env_file:
      - ./env/backend.env
    depends_on:
      ma20240469-db:
        condition: service_healthy
    networks:
      - ma20240469-network

  ma20240469-front:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: ma20240469-front
    ports:
      - "7000:7000"
    volumes:
      - /app/node_modules
      - ./frontend:/app
    env_file:
      - ./env/frontend.env
    depends_on:
      - ma20240469-back
    networks:
      - ma20240469-network

  ma20240469-db:
    image: mysql:8.0
    container_name: ma20240469-db
    volumes:
      - mysql-data:/var/lib/mysql
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql
      - ./db/my.cnf:/etc/mysql/conf.d/my-custom.cnf:ro
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "student", "-pstudent"]
      interval: 10s
      timeout: 10s
      retries: 6
    networks:
      - ma20240469-network

networks:
  ma20240469-network:
    name: ma20240469-network
    driver: bridge

-----------------------------------------------------------------------------

version: '3.8'

name: ma20240469-jui

services:
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: ma20240469-server
    ports:
      - "5000:5000"
    volumes:
      - /app/node_modules
      - ./backend:/app
    env_file:
      - ./env/backend.env
    depends_on:
      database:
        condition: service_healthy
    networks:
      - ma20240469-mreza

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: ma20240469-app
    ports:
      - "7000:7000"
    volumes:
      - /app/node_modules
      - ./frontend:/app
    env_file:
      - ./env/frontend.env
    depends_on:
      - backend
    networks:
      - ma20240469-mreza

  database:
    image: postgres:16-alpine
    container_name: ma20240469-baza
    volumes:
      - ma20240469-postgres-data:/var/lib/postgresql/data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql:ro
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U student -d games"]
      interval: 10s
      timeout: 10s
      retries: 6
    networks:
      - ma20240469-mreza

networks:
  ma20240469-mreza:
    name: ma20240469-mreza
    driver: bridge

volumes:
  ma20240469-postgres-data:

volumes:
  mysql-data:
