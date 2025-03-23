---
title: Deployment with Heroku CLI
date: 2021-06-03 12:00:00 -0500
categories: [Deployment, Heroku]
tags: [Heroku, Git, Database]
---

## Deployment with Heroku CLI

1. Install Heroku CLI

   **Ubuntu / Debian apt-get**

   ```
   $ curl https://cli-assets.heroku.com/install-ubuntu.sh | sh
   ```

   **npm**

   ```
   $ npm install -g heroku
   ```

   **Check installed version**

   ```
   $ heroku --version
   ```

2. Login Heroku

   ```
   $ heroku login
   ```

   Close browser after login success

3. Push code to Heroku

   ```
   $ heroku git:remote -a paoloc-nomadcoffee-backend
   $ git push heroku master
   ```

4. Check Server logs

   ```
   $ heroku logs --tail
   ```

## Prisma Migrate to Heroku

Now a the server is deployed on Heroku, but database does not exist on the server. By following these steps, you will add a `Postgres` database and get `migrate` of the database schema.

1. Go to `Resources` tap

2. Search `Postgres` on Add-ons search and choose `Heroku Postgres`
   Select the `Plan name` (**Hobby Dev - Free**)

3. Add `Procfile` on the root

   ```
   release: npx prisma migrate deploy
   web: npm start
   ```

4. Push the changes to Heroku

   ```
   $ git push heroku master
   ```

## Add GitHub Deployment method

This configuration will deploy the heroku server automatically when the master branch of the project repository updated.

1. Go to `Deploy` tap

2. Select `GitHub` on Deployment method

3. Add App connected to GitHub and select project repository

4. Click `Enable Automatic Deploys` on Automatic deploys

## References:

- [The Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)
- [Deploying database changes with Prisma Migrate](https://www.prisma.io/docs/guides/deployment/deploy-database-changes-with-prisma-migrate)
