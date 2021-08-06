# Stage 1
FROM python:3.7
# Copy src
COPY templates ./templates
COPY mysql-operator.py ./mysql-operator.py
# Install dep's
RUN pip install \
    kopf kubernetes pyyaml jinja2
# Runtime
CMD kopf run /mysql-operator.py
