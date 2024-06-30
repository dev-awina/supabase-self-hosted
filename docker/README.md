# Supabase Docker

This is a minimal Docker Compose setup for self-hosting Supabase. Follow the steps [here](https://supabase.com/docs/guides/hosting/docker) to get started.



## Tutorial

This setup follows the tutorial of activenode: https://blog.activeno.de/the-ultimate-supabase-self-hosting-guide
The main customizations are:

- We use custom S3 Bucket
- We use OAuth providers

## Custom setup

We are going to use docker compose to run our supabase.
We use `docker compose -f docker-compose.yml -f docker-compose.s3.yml up` in order to run the app with our custom S3 bocket. Therefore we need the respective env vars to be in place.

## Next steps

- Secure Ports and go with authelia