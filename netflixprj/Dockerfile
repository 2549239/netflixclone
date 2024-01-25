# Use an official Python runtime as a parent image
FROM python:3.11.4-slim

# Set the working directory in the container
WORKDIR /app

# Install system dependencies
RUN apt-get update && \
    apt-get install -y libpq-dev

# Copy the current directory contents into the container at /app
COPY . /app

# Install pip in Ubuntu
RUN apt update && \
    apt install -y python3-pip

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 8000 available to the world outside this container
EXPOSE 8000

# Set the PYTHONPATH to include the directory containing your Django project
ENV PYTHONPATH="/app/netflixclone/netflixprj"

# Define environment variable
ENV DJANGO_SETTINGS_MODULE="netflixprj.settings"

# Set the STATIC_ROOT
ENV STATIC_ROOT="/app/netflixclone/netflixprj/static/assets/"

# Combine migrations, collect static files, and CMD in a single RUN instruction
RUN python manage.py migrate && \
    python manage.py collectstatic --noinput

# Run app.py when the container launches
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
