# Push Your Ansible Role to Ansible Galaxy

## 1. Create the Role Structure

Ensure your role follows the standard Ansible role directory structure.

```text
my_role/
├── defaults/
│   └── main.yml
├── files/
├── handlers/
│   └── main.yml
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
├── tests/
│   ├── inventory
│   └── test.yml
└── vars/
    └── main.yml
```

## 2. Verify the Ansible Galaxy CLI

Check that the `ansible-galaxy` command is installed.

```bash
ansible-galaxy --version
```

## 3. Push Your Role to GitHub

Initialize a Git repository, commit your role, and push it to GitHub.

```bash
cd <role-name>

git init
git remote add origin https://github.com/<your_github_username>/<role-name>.git
git add .
git commit -m "Initial commit"
git branch -M main
git push -u origin main
```

## 4. Import the Role into Ansible Galaxy

Import the GitHub repository into Ansible Galaxy.

```bash
ansible-galaxy role import <your_github_username> <role-name> --token <github_api_token>
```

Once the import is successful, your role will be available for installation using:

```bash
ansible-galaxy role install <your_github_username>.<role-name>
```