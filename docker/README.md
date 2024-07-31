# Supabase Docker

This is a minimal Docker Compose setup for self-hosting Supabase. Follow the steps [here](https://supabase.com/docs/guides/hosting/docker) to get started.

## Connect to VM
`ssh ubuntu@<ip-address> -i ~/.ssh/id_ed25519_awina_flow_vm`

`git clone https://github.com/dev-awina/supabase-self-hosted.git --depth=1` Don't forget the switch the custom docker compose branch. Currently you might be missing the branch due to default git config. So run these:

- `git config remote.origin.fetch "+refs/heads/*:refs/remotes/origin/*"`
- `git fetch --all`
- `git checkout custom-docker-compose`

## Install docker

https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository


## Tutorial

This setup follows the tutorial of activenode: https://blog.activeno.de/the-ultimate-supabase-self-hosting-guide
The main customizations are:

- We use custom S3 Bucket
- We use OAuth providers

## Set environment variables

Initially you have to `cp .env.example .env` and set the the correct and safe environment variables. Also you have to set your domain in various places. Search for `heytoki` in this project to find all those occurences.

## Custom setup

We are going to use docker compose to run our supabase.
We use `docker compose -f docker-compose.yml -f docker-compose.s3.yml up -d` in order to run the app with our custom S3 bocket. Therefore we need the respective env vars to be in place.

## Configuring Proxy

Open <ip-address>:81 and login with admin@example.com and password "changeme". Change credentials and first of all secure the nginx proxy manager itself as described here: https://blog.activeno.de/the-ultimate-supabase-self-hosting-guide#heading-manage-nginx

## Secure with authelia

For a two-factor setup with authelia you have to check the 'authelia/config/notifications.txt' to see the delivered email content that contains the respective OTP to sync that stuff e.g. with a mobile device using some auhtenticator app.

## Web Client setup

It needs to be decided on how to operate the web client. Either go with some kind of propreatiery web hosting like vercel or go with a docker compose setup also. In that case the docker should reference an image that needs to be uploaded to an accessible container registry beforehand automtically. Also the Github CICD would have to restart that container every time a new image is published... Let's see :)


## Pitfalls

#### Storage is not accessible 

This error occured when the Supabase Keys JWT_SECRET, ANON_KEY, SERVICE_ROLE_KEY have not been set properly. Go to docs and use the generated ones from there.

#### Analytics does not start properly

Analytics server is broken when it gets confused with pre existing data. Just remove all data: sudo rm -rf volumes/db/data/ (But this is actually not nice, you need to safe data beforehand) `sudo docker compose restart`



#### Reset database

1. Dump data from the db like this:  
`npx supabase db dump -s public -f supabase/seed.sql --db-url "postgres://postgres:{{pw}}@{{ip}}:5432/postgres"`
2. Stop all containers like this: `sudo docker compose -f docker-compose.yml -f docker-compose.s3.yml down`
2. Remove all the db volumes like this: `cd volumes/db && sudo rm -rf /data && cd ../..`
2. Start all containers again like this: `sudo docker compose -f docker-compose.yml -f docker-compose.s3.yml up -d`
3. Restart all containers and push the dumped data again: `npx supabase db push --include-seed --db-url "postgres://postgres:${{ secrets.POSTGRES_PASSWORD_PROD }}@${{ secrets.SERVER_IP_PROD }}:5432/postgres`
