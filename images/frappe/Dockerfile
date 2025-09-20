FROM frappe/frappe-worker:v15.latest

# Switch to frappe user to install the LMS app
USER frappe

# Get Frappe LMS from GitHub (this happens during Docker build)
RUN cd /home/frappe/frappe-bench && \
    bench get-app lms https://github.com/frappe/lms.git

# Switch back to root for system configurations
USER root

# Copy custom configuration files
COPY ./config/nginx-template.conf /templates/nginx/frappe.conf.template
COPY ./config/nginx-entrypoint.sh /usr/local/bin/nginx-entrypoint.sh
COPY ./config/supervisor.conf /etc/supervisor/conf.d/frappe.conf

# Install required system packages
RUN apt-get update \
    && apt-get install --no-install-recommends -y \
    sudo \
    supervisor \
    jq \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Redli client for Redis TLS connections
RUN curl -L https://github.com/IBM-Cloud/redli/releases/download/v0.15.0/redli_0.15.0_linux_amd64.tar.gz -o redli.tar.gz && \
    tar xzf redli.tar.gz && \
    mv redli_linux_amd64 /usr/local/bin/redli && \
    chmod +x /usr/local/bin/redli && \
    rm redli.tar.gz

# Make the entrypoint script executable
RUN chmod +x /usr/local/bin/nginx-entrypoint.sh

# Expose the required ports
EXPOSE 8000 8080 9000

# Set the entrypoint
ENTRYPOINT ["nginx-entrypoint.sh"]
