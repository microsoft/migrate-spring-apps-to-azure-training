FROM mcr.microsoft.com/java/maven:8-zulu-debian9

RUN apt update -y && apt upgrade -y \
    && apt install apt-utils -y \
    && apt install wget -y \
    && apt install git -y \
    && apt install nano -y \
    && apt install procps -y \
    && apt install jq -y

# Install Azure CLI
RUN apt-get install ca-certificates curl apt-transport-https lsb-release gnupg -y  \
    && curl -sL https://packages.microsoft.com/keys/microsoft.asc | \
        gpg --dearmor | \
        tee /etc/apt/trusted.gpg.d/microsoft.asc.gpg > /dev/null \
    && AZ_REPO=$(lsb_release -cs) \
    && echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        tee /etc/apt/sources.list.d/azure-cli.list \
    && apt-get update -y \
    && apt-get install azure-cli -y \
    && apt autoremove -y

# Install extensions

RUN az extension add --name spring-cloud -y
RUN az extension add --name db-up -y 

# Install MySQL CLI
RUN apt-get install mysql-client -y  && \
    apt autoremove -y

# Install PostgreSQL CLI
RUN apt-get install postgresql-client -y  && \
    apt autoremove -y

#
# Install Docker CLI
#
RUN apt-get update -y && \
    apt-get install -y apt-transport-https ca-certificates curl gnupg2 \
        software-properties-common && \
    curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - && \
    add-apt-repository \
        "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" && \
    apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y docker-ce-cli && \
    apt autoremove -y

#
# Install Docker compose
#
RUN curl -L "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" \
    -o /usr/local/bin/docker-compose && \
    chmod +x /usr/local/bin/docker-compose

#
# Install kubectl
#
RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl && \
    chmod +x ./kubectl && \
    mv ./kubectl /usr/local/bin/kubectl

RUN mkdir /lab

ENTRYPOINT [ "bash", "-c", "tail -f /dev/null" ]
