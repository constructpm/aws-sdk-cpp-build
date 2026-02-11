# Prebuilt aws-sdk-cpp

This project builds the official [aws-sdk-cpp](https://github.com/aws/aws-sdk-cpp) binaries for Ubuntu 22.04 using GitHub actions runners.

## How to use this repo

So you need to build a specific version of aws-sdk-cpp. Here's how:

1. create a branch for the version you want (use whatever name seems sensible)
2. update `.github/workflows/main.yml` to specify the version of aws-sdk-cpp you want to build
3. create a PR with your changes. This will trigger the CI to do a test build
4. debug any issues
    1. check the action suceeded
    2. check the artefacts it built are correct/work for you
    3. keep tweaking `main.yml` until the action builds the version you want successfully
5. once you have a successful build, merge/rebase your PR into `main`
6. Then tag the latest commit on `main` with the aws-sdk-cpp version number to trigger a release build (`git tag v1.11.746 && git push --tags`)

Now to use the library you have built do something like:

Use `curl` to download and extract to `/opt/aws-sdk-cpp`:

    curl -L https://github.com/constructpm/aws-sdk-cpp-build/releases/download/v1.11.746/aws-sdk-cpp-1.11.746-$PLATFORM.tar.gz | sudo tar -xJC /opt

where `$PLATFORM` is currently restricted to `ubuntu-22.04`.


## Modules

We only build the modules that we need from aws-sdk-cpp. This reduces build times and binary sizes.
This can be changed using the `aws_sdk_cpp_modules` env in the actions workflow.
Currently the modules we use are:

- `s3`


## Details

Here's some more info about how this repo works. It's basically just a workflow that pulls the aws-sdk-cpp source code for a specific version, builds it, and then optionally produces a release.

### The build

The workflow will pull the aws-sdk-cpp source code from the `aws/aws-sdk-cpp` github repo. 
We use the tags on this repo (eg `1.11.746`) when checking out to make sure we checkout a specific aws-sdk-cpp version.
The aws-sdk-cpp version specified at the top of the workflow file is this tag.

### Trigger the build

To trigger a build just create a PR and push to it. Every push will build aws-sdk-cpp and provide the results as artefacts tied to the workflow run.

### Trigger the release

To trigger a release just push a tag that starts with `v`. Ideally push a tag matching the version of aws-sdk-cpp you want to build a release for. The release will use the tag you provide to both tag and name the release (so tag `v1.2.3` will produce `Release v1.2.3` with tag `v1.2.3`).
Simply pushing a tag will trigger the build process (on any branch), and then will create a Release using the result of that build. That release can be used via the `curl` process listed above.
