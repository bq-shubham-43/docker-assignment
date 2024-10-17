FROM python:3.9-slim
WORKDIR /app
COPY . /app
RUN apt-get update && apt-get install -y curl
RUN pip install Flask
EXPOSE 5000
CMD ["python", "app.py"]
