name: ma20240469-app

services:
  backend:
    image: ma20240469-backend
    container_name: ma20240469-back
    ports:
      - "7000:5000"
    env_file:
      - env/backend.env
    volumes:
      - /app/__pycache__
      - ./:/app
    networks:
      - pg20220043-network
    depends_on:
      database:
        condition: service_healthy

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: ma20240469-front
    ports:
      - "3000:3000"
    env_file:
      - env/frontend.env
    volumes:
      - /app/node_modules
      - ./frontend:/app
    networks:
      - pg20220043-network
    depends_on:
      - backend

  database:
    image: postgres:16-alpine
    container_name: pg20220043-db
    env_file:
      - env/db.env
    volumes:
      - mysql-data:/var/lib/postgresql/data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql
      - ./db/my.cnf:/etc/mysql/conf.d/my-custom.cnf
    networks:
      - ma20240469-network
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "student"]
      interval: 10s
      timeout: 10s
      retries: 6

volumes:
  mysql-data:

networks:
  ma20240469-network:
    name: ma20240469-network
