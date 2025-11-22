ARG VARIANT=1.91_3.12
FROM registry.gitlab.com/wizrds/rust-python-docker:${VARIANT}

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    # Remove imagemagick due to https://security-tracker.debian.org/tracker/CVE-2019-10131
    && apt-get purge -y imagemagick imagemagick-6-common \
    && apt-get install -y jq bat curl git zsh locales \
    && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen \
    && update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8 \
    && rm -rf /var/lib/apt/lists/*

ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

# Create a non-root user and give necessary permissions
# We use the UID 1000 since it is the default UID for the first user on most systems
RUN useradd -u 1000 -G sudo -U -m -s /usr/bin/zsh dev \
    && echo "dev ALL=(ALL) NOPASSWD: /bin/chown" >> /etc/sudoers

# Temporary: Upgrade python packages due to https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-40897 and https://github.com/advisories/GHSA-2mqj-m65w-jghx
# They are installed by the base image (python) which does not have the patch.
RUN python3 -m pip install --upgrade \
    setuptools==69.0.3 \
    gitpython==3.1.41 \
    uv

# Install Taskfile
RUN sh -c "$(curl --location https://taskfile.dev/install.sh)" -- -d -b /usr/local/bin
COPY Taskfile.yml /home/dev/Taskfile.yml
ADD .taskfile /home/dev/.taskfile
# Install dev tools
RUN task -d /home/dev devtools:install

# Install zsh-in-docker
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.2.1/zsh-in-docker.sh)" -- \
    -t lambda

# Switch to the non-root user
USER dev
