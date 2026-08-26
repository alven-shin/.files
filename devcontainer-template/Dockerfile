FROM ubuntu:latest

# Host UID/GID get passed in at build time so files created in the
# container (or already on the host) line up with your host user.
# Defaults (1000:1000) match most Linux distros; override via compose
# or --build-arg on macOS, where the default user is often 501:20.
ARG USER_UID=1000
ARG USER_GID=1000
ARG USERNAME=dev

ENV DEBIAN_FRONTEND=noninteractive

# Basic dev tooling — trim or extend to taste.
RUN apt-get update && apt-get install -y --no-install-recommends \
        sudo \
        ca-certificates \
        curl \
        git \
        build-essential \
        vim \
        less \
    && rm -rf /var/lib/apt/lists/*

# Create a non-root user whose UID/GID match the host user, and give
# it passwordless sudo for convenience inside the dev environment.
#
# Newer Ubuntu base images ship a built-in 'ubuntu' user/group at
# 1000:1000. If USER_UID/USER_GID collide with it, `userdel -r` below
# will also remove that user's private group as a side effect — so we
# explicitly delete *both* the conflicting user and group ourselves
# rather than relying on an earlier existence check that a later step
# might invalidate.
RUN set -eux; \
    if getent passwd "${USER_UID}" >/dev/null 2>&1; then \
        userdel -r "$(getent passwd "${USER_UID}" | cut -d: -f1)" 2>/dev/null || true; \
    fi; \
    if getent group "${USER_GID}" >/dev/null 2>&1; then \
        groupdel "$(getent group "${USER_GID}" | cut -d: -f1)" 2>/dev/null || true; \
    fi; \
    groupadd --gid "${USER_GID}" "${USERNAME}"; \
    useradd --uid "${USER_UID}" --gid "${USER_GID}" -m -s /bin/bash "${USERNAME}"; \
    echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/${USERNAME}; \
    chmod 0440 /etc/sudoers.d/${USERNAME}

USER ${USERNAME}
WORKDIR /workspace

CMD ["/bin/bash"]
