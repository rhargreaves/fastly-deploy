# fastly-deploy

A tool to assist in the deployment of updated VCL to Fastly. Provides additional verification that the new VCL has taken effect.

**Note: This repository is no longer updated. See https://github.com/7digital/fastly-deploy for a more up-to-date and actively maintained version of this app.**

## Usage
```
deploy.rb [options]
    -k, --api-key API_KEY            Fastly API Key
    -s, --service-id SERVICE_ID      Service ID
    -v, --vcl-path FILE              VCL Path
    -p, --purge-all                  Purge All
    -h, --help                       Display this screen
```

## Example

```
ruby bin/deploy.rb -k d3cafb4dde4dbeef -s 123456789abcdef -v foo.vcl -p
```

## Versioning

The deployment process clones the current activated version, as opposed to the latest/highest numbered version.

## Deployment Verification

After the new service version has been activated, an optional check can be made against the service URL to ensure that the new VCL has actually taken effect. In order to facilitate this, the VCL file must have the following `#DEPLOY` directives present so that additional logic can be injected before the upload:

```
sub vcl_recv {
    #DEPLOY recv
    ...
}

sub vcl_error {
    #DEPLOY error
    ...
}
```

The directives inject code that cause the VCL to emit a synthetic response containing the newly activated service version number when a request is made against `/vcl_version`. After deployment, this URL is polled until the version number matches that of the new deployment.

If the directives are not present, the additional verification check is skipped.

## Testing

Tests can be ran against a Fastly account by populating the `FASTLY_TEST_API_KEY` environment variable with a Fastly API key and running `rspec`. As part of the tests, services will be automatically created and deleted by the fixtures. Do not run the tests using an account shared with production services.
