# 🏃‍♂️ GitHub Actions Runner Setup on Raspberry Pi

This repository contains an Ansible-based setup to configure a Raspberry Pi 4/5 (Ubuntu 22.04) as a self-hosted GitHub Actions runner. It is designed to be run from a remote host (e.g. Mac) that uses Ansible to provision the Raspberry Pi.

## 💡 Objective

Use a Raspberry Pi as a local CI/CD server to execute GitHub Actions workflows. This allows workflows to run on real ARM hardware and interact with your local network or other devices.

## 📦 Requirements

- Raspberry Pi 4 or 5 with Ubuntu 22.04 installed
- SSH access to the Raspberry Pi
- Your public SSH key added to the Pi
- GitHub repository where the runner should register
- A Mac (or other host) with Ansible installed

## 🔧 Setup Instructions

### 1. From your Mac (or control machine):

- Install Ansible:
  ```bash
  brew install ansible
  ```

- Clone this repository:
  ```bash
  git clone git@github.com:AlZi-88/runner_setup.git
  cd runner_setup
  ```

- Add your Raspberry Pi to the Ansible inventory:
  Edit `ansible/inventory/compi.yaml`:
  ```yaml
  all:
    hosts:
      compirunner:
        ansible_host: runner
        ansible_user: cmopi
  ```

- Ensure you can SSH into the Pi without a password:
  ```bash
  ssh-copy-id compi@runner
  ```

### 2. Generate GitHub Token

- Go to your GitHub repository
- Navigate to Settings > Actions > Runners > Add Runner
- Select Linux, ARM64 and copy the URL and token
- Optional: Save them as environment variables or directly use in `roles/github_runner/tasks/main.yml`

### 3. Run the Playbook

- Execute the playbook to install and register the runner from `runner_setup`-folder:
  ```bash
  ansible-playbook -i ansible/inventory/compi.yaml ansible/playbook-runner.yml --ask-become-pass
  ```

### 4. Confirm Runner Is Online

- In your GitHub repository, go to Settings > Actions > Runners
- You should see the runner `compi` listed as idle and ready

## ✅ Result

Your Raspberry Pi now serves as a GitHub Actions runner. You can run workflows using:

```yaml
runs-on: [self-hosted]
```

## 🧪 Testing

- Add a test workflow to your GitHub repo to verify the runner is functioning:
  1. Push a `.github/workflows/test.yml` with a simple `echo` step
  2. Run the workflow manually or on push

## 📁 Structure

```
ansible/
├── inventory/
│   └── compirunner.yaml
├── roles/
│   └── github_runner/
│       └── tasks/main.yml
├── playbook-runner.yml
README.md
```

## 📌 Notes

- The GitHub token used for registration is usually valid for a short period
- You can extend this setup to deploy multiple runners or manage other devices

---
