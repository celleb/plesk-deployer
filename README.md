# Plesk Deployer Action

[![test](https://github.com/celleb/plesk-deployer/actions/workflows/test.yml/badge.svg)](https://github.com/celleb/plesk-deployer/actions/workflows/test.yml)

This GitHub Action deploys applications to a Plesk server using SCP. If your project is using Node.js, this action will also install dependencies and restart the application.

## Inputs

- `ssh-private-key`: **Required**. SSH private key for authentication.
- `ftp-username`: **Required**. FTP username for the Plesk server.
- `ftp-server`: **Required**. FTP server address.
- `files-to-copy`: **Required**. Files or directories to copy to the server.
- `remote-dir`: **Optional**. Remote directory to copy files to. Defaults to `/httpdocs`.
- `node-version`: **Optional**. Node.js version to use. Defaults to `20`.
- `package-manager`: **Optional**. Package manager to use (`npm` or `yarn`). Defaults to `npm`.
- `npm-install`: **Optional**. Whether to install dependencies. Defaults to `true`.
- `restart`: **Optional**. Whether to restart the application. Defaults to `true`.
- `clean-remote-dir`: **Optional**. Whether to clean the remote directory before deployment. Defaults to `false`.

## Outputs

- `deployment-status`: Status of the deployment.

## Example Usage

Here's an example of how to use the Plesk Deployer Action in a GitHub Actions workflow with npm:

```yaml
name: Deploy to Plesk

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 20.x

      - name: Install dependencies
        run: npm ci

      - name: Build application
        run: npm run build

      - name: Deploy to Plesk
        uses: celleb/plesk-deployer@v1
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
          ftp-username: ${{ vars.FTP_USERNAME }}
          ftp-server: ${{ vars.FTP_SERVER }}
          files-to-copy: "./dist ./package.json ./package-lock.json"
          remote-dir: "./httpdocs"
          node-version: 20
          package-manager: npm
          npm-install: true
          restart: true
          clean-remote-dir: true
```

And here's an example using Yarn:

```yaml
name: Deploy to Plesk

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 20.x

      - name: Install dependencies
        run: yarn install --frozen-lockfile

      - name: Build application
        run: yarn build

      - name: Deploy to Plesk
        uses: celleb/plesk-deployer@v1
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
          ftp-username: ${{ vars.FTP_USERNAME }}
          ftp-server: ${{ vars.FTP_SERVER }}
          files-to-copy: "./dist ./package.json ./yarn.lock"
          remote-dir: "./httpdocs"
          node-version: 20
          package-manager: yarn
          npm-install: true
          restart: true
          clean-remote-dir: true
```

## Help Section

### Generating an SSH Key and Adding it to GitHub Secrets

1. **Generate an SSH Key:**

   - Open a terminal and run: `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
   - Follow the prompts to save the key, typically in `~/.ssh/id_rsa`.

2. **Add the Private Key to GitHub Secrets:**
   - Copy the contents of your private key file (e.g., `~/.ssh/id_rsa`).
     You can use the following command to copy the contents to your clipboard:
     ```
     cat ~/.ssh/id_rsa | pbcopy
     ```
   - Go to your GitHub repository, navigate to **Settings** > **Secrets and variables** > **Actions** > **New repository secret**.
   - Name the secret `SSH_PRIVATE_KEY` or whatever you want and paste the private key content.

### Adding Variables in GitHub Repository

1. **Navigate to Repository Settings:**

   - Go to your GitHub repository, navigate to **Settings** > **Secrets and variables** > **Actions** > **Variables**.

2. **Add a New Variable:**
   - Click on **New repository variable**.
   - Enter the variable name (e.g., `FTP_USERNAME`) and its value.
   - Repeat for any additional variables needed.

### Creating an FTP User on Plesk and Adding Your Public Key

1. **Create an FTP User:**

   - Log in to your Plesk panel.
   - Go to **Websites & Domains** > **FTP Access**.
   - Click **Add an FTP Account** and fill in the required details.

2. **Add Your Public Key to the Server:**
   - Copy the contents of your public key file (e.g., `/path/to/your/public/key.pub`).
   - Use the `ssh-copy-id` command to add your public key to the server:
     ```
     ssh-copy-id -i /path/to/your/public/key.pub username@your_server_hostname
     ```
   - Replace `/path/to/your/public/key.pub` with the actual path to your public key file.
   - Replace `username` with your FTP username and `your_server_hostname` with your server's hostname or IP address.

## Troubleshooting Plesk Node.js Deployments

### Common Issues

1. **Delete Default Files:**

   - Ensure that any default `index.html` or `index.php` files are removed from the deployment directory (`/httpdocs` or your specified `remote-dir`). These files can interfere with Node.js applications.

2. **Verify Node.js Installation:**

   - Make sure that Node.js and the specified version are installed and enabled for your domain in Plesk. You can check this in the Plesk panel under **Websites & Domains** > **Node.js**.

3. **Check Passenger Logs:**
   - If your application is not starting correctly, check the Passenger logs for errors. These logs can provide insights into what might be going wrong. Logs are typically located in the `/var/log/passenger/passenger.log` directory of your domain or accessible via the Plesk panel. You stream the logs to the console with `tail -f /var/log/passenger/passenger.log`

## Contributing

Feel free to open issues or submit pull requests for improvements or bug fixes.

## License

This project is licensed under the MIT License.
