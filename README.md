# Docker ECR Mirror

Mirror public docker images to ECR, automagically. This requires [Skopeo](https://github.com/containers/skopeo) to be installed.

## Usage

Create an ECR repository with the following two tags set:

* `upstream-image` set to a public Docker hub image, i.e `nginx` or `istio/proxyv2`
* `upstream-tags` set to a `/`-separated list of tag **globs**, i.e `1.6.*` or just `1.2-alpine`. ECR does not allow the
  use of the `*` character in tag values, so you should use `+` as a replacement.

You can select a range of different tags like so: `1.1[4567]*` - this will match `1.14` to `1.17`.

Terraform example:

```hcl-terraform
resource "aws_ecr_repository" "repo" {
  name = "nginx"
  tags = {
    upstream-image = "nginx",
    // Mirror 1.16* and 1.17+
    upstream-tags = "1.16+/1.17+"
  }
}
```

Running `ecr-mirror sync` will begin concurrently fetching all images and pushing them to ECR.

You can run `ecr-mirror list` to see all repositories that will be mirrored.

You can also manually copy specific image patterns using `ecr-mirror copy`:

`ecr-mirror copy "istio/proxyv2:1.6.*" ACCOUNT_ID.dkr.ecr.eu-west-1.amazonaws.com/istio-proxyv2`
