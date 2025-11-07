# Code Server Setup

Quick setup for code-server with docker-compose.

## One-Line Setup

Run this command to create `docker-compose.yml` and `code-server-config/config.yaml`:

```bash
cat > docker-compose.yml << 'EOF'
services:
    code-server:
        image: sarowar18/code-server:latest
        container_name: code-server
        environment:
            # - PASSWORD=changeme123
            - auth=none
            - VSCODE_PROXY_URI=http://localhost:4040/proxy?url=http://localhost:{{port}}
            - PORT=4080
            - CODE_INTERNAL_PORT=4081
            - PORT_RANGE_START=4000
            - PORT_RANGE_END=4020
            - CODE_SERVER_REPO_BASE=/home/coder/repos
        volumes:
            - ./repos:/home/coder/repos
            - ./code-server-config:/home/coder/.config/code-server
        ports:
            - "4080:4080"
            - "4081:4081"
            - "4000-4020:4000-4020"
            - "4090:4090"
        restart: unless-stopped
    clone-server:
        image: sarowar18/clone-server:latest
        container_name: clone-server
        volumes:
            - ./repos:/app/repos
            - ~/.ssh:/root/.ssh:ro
        ports:
            - "4040:4040"
        environment:
            - SSH_AUTH_SOCK=/tmp/ssh-agent.sock
            - GITHUB_TOKEN=${GITHUB_TOKEN}
            - PORT=4040
            - PUBLIC_PORT=4000
            - CODE_SERVER_PORT=4080
            - CODE_SERVER_DIRECT_PORT=4081
            - PORT_RANGE_START=4000
            - PORT_RANGE_END=4020
        restart: unless-stopped
EOF
mkdir -p code-server-config && cat > code-server-config/config.yaml << 'EOF'
bind-addr: 0.0.0.0:4000
auth: none
password: d69ff7e989fe3aec29deb735
cert: false
EOF
```

## Usage

1. Run the setup command above
2. Set your `GITHUB_TOKEN` environment variable (optional, for private repos)
3. Start the services:
   ```bash
   docker-compose up -d
   ```
4. Access code-server at `http://localhost:4080`

## Notes

- Make sure Docker and Docker Compose are installed
- The `repos` directory will be created automatically on first run
- SSH keys in `~/.ssh` are mounted read-only for git operations
