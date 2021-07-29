# Fluree w/ S3 Persisitence [via Docker Compose]

This project provides an example of how to serve a dockerized Fluree instance, specifically using an S3 bucket instead of a locally-available file system (or EFS, NFS, etc) for file persistence.

This example will use Docker Compose, but this could be done with the Docker CLI or with a local Fluree instance provisioned with the same AWS & Fluree environment variables

## Get Started

Clone or download this repository onto your machine.

```
git clone https://github.com/fluree/example-s3-ledger.git

cd example-s3-ledger
```

### AWS Config

To run Fluree on top of an S3 bucket, you'll need an **AWS access key** and **AWS secret access key** with read/write permissions to an **S3 bucket**.

The project directory provides an example `.aws` folder with `config` and `credentials` files, following standard practice for persisting AWS config on your local machine for use with the AWS CLI.

Feel free to configure your own local machine's `.aws` config/credentials instead of using the provided example in this repo (in which case you will also need to update `docker-compose.yaml` to reflect the new directory location for the `.aws` volume mount described by `services.fluree.volumes` ).

This README will assume you are setting AWS config and credentials within the local directory as provided by this repo.

Ensure the following:

- `/.aws/config`
  - You have a profile name (e.g. `[profile fluree-sandbox]`) that matches the `AWS_DEFAULT_PROFILE` value within `docker-compose.yaml`'s `services.fluree.environment` vars
- `/.aws/credentials`
  - You have a profile name (e.g. `[fluree-sandbox]`) that matches the profile name in `./aws/config` and that includes keys that are capable of read/write to your bucket
- `docker-compose.yaml`
  - Your `FDB_STORAGE_S3_BUCKET` value in your `services.fluree.environment` vars should be the name of your S3 bucket (e.g. `fluree-s3-test`)

### Customizing Fluree

Fluree will work without changing any of its configuration. However, if you want to change any of Fluree's configuration, you can do so by providing additional env vars within the `docker-compose.yaml` file.

Information regarding configurable Fluree settings is available online. The `Getting Started -> Installation` section provides insight on installing and customizing Fluree (https://docs.flur.ee/docs/getting-started/installation).

### Running Fluree

Once you've provided necessary AWS config and any additional Fluree config, you can start Fluree by running the following from the directory where you cloned the repo.

```
docker-compose up
```

You can then navigate to `http://localhost:8090` to see Fluree's AdminUI interface.

After adding a new ledger, you should immediately begin to see your S3 bucket populated with the persistent files necessary for Fluree to maintain its ledger data and RAFT state.

### Troubleshooting

Note that Fluree should be allowed to gracefully shutdown whenever possible in order to preserve its most recent RAFT state snapshot. As such, you should allow your docker container to gracefully shutdown rather than immediately killing the process.

When running from the command line, this means using `docker-compose stop` or `docker stop CONTAINER_ID` rather than, for example, killing the thread if the container is running in the foreground of your terminal.

## License

This project is licensed under the terms of the MIT license.
