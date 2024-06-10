# Directus Starter

This is the starter template that I use when creating a new project with Directus. I find configuring Directus from scratch each time very annoying, so I created this template to save time.

- [Directus Starter](#directus-starter)
  - [What is Directus?](#what-is-directus)
  - [Features](#features)
  - [Prerequisites](#prerequisites)
  - [Getting Started](#getting-started)
  - [Configuration](#configuration)
  - [Directus Sync](#directus-sync)
  - [Bonus](#bonus)
  - [Contributing](#contributing)

## What is Directus?

Directus is an open-source Data Platform that allows you to instantly create a headless CMS, which can be used to manage content and data in your applications. This starter project aims to provide a quick and easy way to get a Directus instance up and running with some basic configurations and sample data.

## Features

- **Directus Installation**: Quick setup for a Directus instance.
- **Configuration Files**: Includes a `.env` file for environment variables.
- **Docker Support**: Docker setup for easy deployment.
- **[Directus Sync](https://github.com/tractr/directus-sync?tab=readme-ov-file#directus-sync)**: Syncs data between Directus instances.

## Prerequisites

Before you begin, ensure you have met the following requirements:

- Node.js (>=18.x)
- Docker
- Git

## Getting Started

To get started, follow these steps:

1. Clone the repository:

```bash
npx --yes giget@latest gh:BayBreezy/directus-starter my-project --install
```

The `--yes` flag will automatically answer yes when `npx` asks if you want to download the latest version of `giget`. The `--install` flag will automatically install the dependencies after the project is created.

The project will be created in a directory called `my-project`.

2. Change into the project directory:

```bash
cd my-project
```

3. Install the dependencies:

```bash
npm install
```

4. Setup the environment variables:

```bash
cp .env.example .env
```

Ensure to update the `.env` file with the necessary configurations.

5. Start the Directus instance:

```bash
npm run start
```

6. Open your browser and navigate to `http://localhost:8055` to access the Directus admin panel.

7. Login with the credentials you provided in the `.env` file (Admin Email and Admin Password).

8. Reinstall the Directus Extension Sync tool:

Go to `Settings` > `Marketplace` and search for `directus-extension-sync`. Install the extension.

9. Restart the Directus instance:

```bash
docker compose down && docker compose up -d
```

Once the instance is up and running, you can run the `sync:pull` command to pull the schema and collections from the Directus instance.

```bash
npm run sync:pull
```

This will create a `directus-config` directory in the root of the project with the schema and collections from the Directus instance.

Be sure to commit this directory to your repository so that you can sync the data between your different environments.

## Configuration

The main configuration file for Directus is the `.env` file located in the root directory. Ensure you update this file with your specific configuration settings.

- **DB_CLIENT**: The database client to use. Default is `pg` for this starter.
- **DB_HOST**: The host of the database. This should be the name given to the service in the `compose.yml` file.
- **DB_PORT**: The port of the database.
- **DB_DATABASE**: The name of the database to use.
- **DB_USER**: The username to use to connect to the database.
- **DB_PASSWORD**: The password to use to connect to the database.
- **ADMIN_EMAIL**: Email for the initial admin user.
- **ADMIN_PASSWORD**: Password for the initial admin user.
- **SECRET**: A secret key for JWT signing.
- **DIRECTUS_URL**: The URL of the Directus instance.
- **DIRECTUS_ADMIN_EMAIL**: Email for the Directus admin user.
- **DIRECTUS_ADMIN_PASSWORD**: Password for the Directus admin user.

## Directus Sync

The Directus Sync tool is included in this starter project. This tool allows you to sync data between Directus instances. You can visit the repo here to learn more about it: [Directus Sync](https://github.com/tractr/directus-sync).

This starter comes with the three commands needed to run the sync tool:

- `sync:pull`: Retrieves the current schema and collections from Directus and stores them locally. This command does not modify the database.
- `sync:push`: Applies the changes from your local environment to the Directus instance. This command pushes your local schema and collection configurations to Directus, updating the instance to reflect your local state.
- `sync:diff`: Analyzes and describes the difference (diff) between your local schema and collections and the state of the Directus instance. This command is non-destructive and does not apply any changes to the database.

## Bonus

I created this workflow to make it easier to deploy the app on your own Ubuntu server. This workflow uses GitHub Actions to deploy the app to your server.

You will need to have docker installed on your server. You can follow the instructions [here](https://docs.docker.com/engine/install/ubuntu/) to install docker on your server.

In your GitHub repository, go to the `Settings` tab and click on `Secrets`. Add a new secret called `PRODENV` and paste the contents of your `.env` file in the value field.

<details>
  <summary>The Workflow</summary>

```yaml
name: Deploy to Ubuntu Server

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: [self-hosted]

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install dependencies
        run: npm install

      - name: Create .env file
        run: echo "${{ secrets.PRODENV }}" > .env

      - name: Run Docker Compose
        run: sudo docker-compose up -d

      - name: Run sync:push script
        run: npm run sync:push
```

</details>

## Contributing

Contributions are welcome! Please follow these steps to contribute:

- Fork the project repository.
- Create a new feature branch (git checkout -b feature/YourFeature).
- Commit your changes (git commit -m 'Add YourFeature').
- Push to the branch (git push origin feature/YourFeature).
- Open a Pull Request.
