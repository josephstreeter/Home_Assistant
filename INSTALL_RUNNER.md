# Installing and Configuring a GitHub Actions Self-Hosted Runner on Your Docker Host

This guide provides step-by-step instructions to install and configure a GitHub Actions self-hosted runner on your Docker host. This allows you to run workflows directly on your server, enabling direct access to your Docker environment and persistent volumes.

---

## Prerequisites

- A GitHub account and repository.
- Access to your Docker host (Linux).
- `curl` and `unzip` installed on your host.

---

## Steps

### 1. Create a Runner in Your GitHub Repository

1. Go to your repository on GitHub.
2. Click on **Settings** > **Actions** > **Runners**.
3. Click **New self-hosted runner**.
4. Select **Linux** as the operating system.
5. Copy the setup commands provided by GitHub.

### 2. Download and Configure the Runner

On your Docker host, run the following commands (replace `<URL>` and `<TOKEN>` with the values from GitHub):

```bash
# Create a directory for the runner
mkdir -p ~/actions-runner && cd ~/actions-runner

# Download the runner package
curl -o actions-runner-linux-x64-2.316.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.316.0/actions-runner-linux-x64-2.316.0.tar.gz

# Extract the package
tar xzf ./actions-runner-linux-x64-2.316.0.tar.gz

# Configure the runner (replace <URL> and <TOKEN>)
./config.sh --url <YOUR_REPO_URL> --token <YOUR_RUNNER_TOKEN>
```

### 3. Install Runner Dependencies

```bash
sudo apt-get update
sudo apt-get install -y libicu-dev
```

### 4. Start the Runner

```bash
./run.sh
```

> **Tip:** To run the runner as a service (recommended for persistence), follow the instructions provided after configuration:
>
> ```bash
> sudo ./svc.sh install
> sudo ./svc.sh start
> ```

### 5. Verify Runner Registration

- Go back to your repository’s **Settings > Actions > Runners**.
- Confirm your runner appears as **Online**.

### 6. Use the Runner in Your Workflow

In your workflow YAML, set:

```yaml
runs-on: self-hosted
```

---

## Maintenance

- To update the runner, repeat the download and extraction steps with the latest version.
- To remove the runner, stop the service and delete the runner directory.

---

## References

- [GitHub Docs: Adding self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/adding-self-hosted-runners)