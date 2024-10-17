# docker-assignment
Steps Taken
1. Docker Compose Configuration:

    Created a docker-compose.yml file to define two containers:
      - Nginx: Acts as a reverse proxy for the Flask app.
      - Flask-app: Hosts the Flask application

            version: '3.8'
            
            services:
              flask-app:
                image: ${FLASK_APP_IMAGE}
                build:
                  context: ./flask-app
                  dockerfile: Dockerfile
                container_name: flask-app
                networks:
                  - my-custom-network
                volumes:
                  - flask-data:/app/data
                ports:
                  - "5000:5000"
                healthcheck:
                  test: ["CMD", "curl", "-f", "http://localhost:5000"]
                  interval: 30s
                  timeout: 10s
                  retries: 3
                restart: always
            
              nginx:
                image: ${NGINX_IMAGE}
                build:
                  context: ./nginx
                  dockerfile: Dockerfile
                container_name: nginx
                depends_on:
                  flask-app:
                    condition: service_healthy
                networks:
                  - my-custom-network
                volumes:
                  - ./nginx/html:/usr/share/nginx/html   # Mount the custom HTML
                  - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf  # Custom Nginx config
                ports:
                  - "80:80"
                command: /bin/sh -c "sleep 10 && nginx -g 'daemon off;'"
            
                restart: "on-failure"
            
            networks:
              my-custom-network:
                driver: bridge
            
            volumes:
              flask-data:
              nginx-data:
2. Environment Variables:
    -  Implemented a .env file to allow the image names for both containers to be specified as environment variables.
              FLASK_IMAGE_NAME=my_flask_app
              NGINX_IMAGE_NAME=my_nginx
3. Custom Bridge Network:
    - Configured both containers to run in a custom bridge network (implicit in Docker Compose).
4. Volume Persistence:
    - Attached separate volumes to both containers to persist their data.
      
        volumes:
          flask-data:/app/data
          nginx-data:/etc/nginx/conf.d
5. Container Dependencies:

    - Set the Nginx container to depend on the Flask app container, introducing a 10-second delay before starting Nginx after the Flask app has been created.
6. Health Checks:

    - Implemented a health check for the Flask app to ensure it is responding on the expected port.
7. Container Management:
    - After running both containers, tested stopping the Nginx container using a terminal command.
      - docker-compose stop nginx
     
8. Automatic Restart Policy:

    - Added a command in the docker-compose.yml file to automatically restart the Flask app container if it fails: 
      - restart: always
 


