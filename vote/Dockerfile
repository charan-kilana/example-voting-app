# base defines a base stage that uses the official python runtime base image
FROM python:3.11-slim AS base

# Add curl for healthcheck
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \

    # What is tini??-----> tini is used to Properly Handle Signals like SIGTERM
    # How do you know curl is required?-----> You should have communication with the DEV team.
    
    rm -rf /var/lib/apt/lists/*
    # This step above is to reduce the image size.
    #It is commonly used in Dockerfiles to reduce the image size by removing package lists 
    # that are no longer needed after installing software.

# Set the application directory
WORKDIR /usr/local/app

# Install our requirements.txt
COPY requirements.txt ./requirements.txt
#copies all dependencies to root directory.

RUN pip install --no-cache-dir -r requirements.txt
# Install dependencies which are in the requirement.txt
# --no-cache-dir → Prevents pip from caching downloaded packages, reducing image size.

# dev defines a stage for development, where it'll watch for filesystem changes
FROM base AS dev
RUN pip install watchdog
#Installs watchdog, a Python library that monitors filesystem changes.
#This is useful in development for auto-reloading when files change.
ENV FLASK_ENV=development
#Sets the FLASK_ENV environment variable to development.
CMD ["python", "app.py"]
#Runs app.py with Python.

# final defines the stage that will bundle the application for production
FROM base AS final

# Copy our code from the current folder to the working directory inside the container
COPY . .

# Make port 80 available for links and/or publish
EXPOSE 80

# Define our command to be run when launching the container
CMD ["gunicorn", "app:app", "-b", "0.0.0.0:80", "--log-file", "-", "--access-logfile", "-", "--workers", "4", "--keep-alive", "0"]
#"prod/final" stage, where we remove unnecessary dev tools and use gunicorn for a fast, optimized Flask app.
