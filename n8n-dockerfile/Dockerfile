###############################################################
# Base image  
###############################################################
FROM ubuntu:24.04

ENV N8N_VERSION=1.123.5
ENV DEBIAN_FRONTEND=noninteractive
ENV PATH=/usr/lib/node_modules/n8n/node_modules/n8n/bin:$PATH

###############################################################
# Install core packages
###############################################################
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    wget \
    git \
    unzip \
    jq \
    ca-certificates \
    gnupg \
    lsb-release \
    apt-transport-https \
    software-properties-common \
    python3 \
    python3-pip

###############################################################
# Install Node.js 22
###############################################################
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get update && apt-get install -y nodejs 

###############################################################
# Install n8n
###############################################################
WORKDIR /usr/lib/node_modules/n8n
COPY package.json .
RUN npm install n8n@${N8N_VERSION}
WORKDIR /

###############################################################
# Install Terraform
###############################################################
RUN wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /usr/share/keyrings/hashicorp.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/hashicorp.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" \
      > /etc/apt/sources.list.d/hashicorp.list \
    && apt-get update && apt-get install -y terraform

###############################################################
# Install AWS CLI
###############################################################
RUN curl -fsSL "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install \
    && rm -rf awscliv2.zip aws

###############################################################
# Install Azure CLI
###############################################################
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

###############################################################
# Install Google Cloud SDK
###############################################################
RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" \
    > /etc/apt/sources.list.d/google-cloud-sdk.list \
    && curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg \
       | gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg \
    && apt-get update && apt-get install -y google-cloud-sdk

###############################################################
# Install Ansible
###############################################################
RUN apt-get update && apt-get install -y --no-install-recommends \
    ansible

###############################################################
# Install Playwright
###############################################################
WORKDIR /opt/playwright
RUN npm install -D @playwright/test@latest \
    && npx playwright install --with-deps
WORKDIR /

###############################################################
# Cleanup
###############################################################
RUN apt-get clean \
    && rm -rf /tmp/* /var/tmp/* /var/lib/apt/lists/* \
    && npm cache clean --force \
    && rm -rf /usr/local/share/.cache /home/node/.cache

###############################################################
# Start n8n
###############################################################
CMD ["n8n"]
