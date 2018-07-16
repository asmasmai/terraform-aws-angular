# Terraform AWS Angular

Create AWS infrastructure resources for Angular application.

## How to use

1.  create a `infra/main.tf` in your project root directory. Replace the `aws.profile` and `spa.hosted_zone`. Do not change the `aws.region`, ACM certificate is required to be in `us-east-1`.

    ```terraform
    # main.tf

    provider "aws" {
        region  = "us-east-1"
        profile = "exampleAdmin"
    }

    module "angular" {
        source        = "github.com/bagubagu/terraform-aws-angular"
        hosted_zone   = "example.com"
        force_destroy = true
    }
    ```

1.  Init and run terraform apply from the `infra` directory

    ```bash
    cd infra
    terraform init
    terraform apply
    ```

1.  Bob's your uncle.

## What does this do?

If hosted_zone is `example_com`:

- Setup s3 originbucket `example-com-origin`
- Setup s3 cloudformation log bucket `example-com-cloudfront-log`
- Add apex and www records to DNS
- Setup cloudfront with alias `example.com` and `www.example.com`
- Configure cloudfront to use lambda@edges
- Create ACM certificate `example.com` and `*.example.com`
- Setup all corresponding IAM users, groups, policies, and roles

### Lambda@Edge Features

- Return everything in `/assets` as is
- Request for uri without extension gets redirected to `/index.html`
- Redirect errors (4xx and 5xx) to `/index.html`
- `stellar.toml` support. CORS is enable on `stellar.toml`. You'll also need to add `/.well-known/stellar.toml` into `apps.assets` in `angular-cli.json`

## FAQ

1.  I got following error

    ```
    Error: Error applying plan:

    1 error(s) occurred:

    * module.spa.aws_cloudfront_distribution.origin: 1 error(s) occurred:

    * aws_cloudfront_distribution.origin: error creating CloudFront Distribution: InvalidViewerCertificate: The specified SSL certificate doesn't exist, isn't in us-east-1 region, isn't valid, or doesn't include a valid certificate chain.
        status code: 400, request id: 68fae058-72bc-11e8-a266-5d955149d452
    ```


    __Answer:__

    It's a bug. Please re-run `terraform apply` and you should be be fine the second time.

## LICENSE

MIT
