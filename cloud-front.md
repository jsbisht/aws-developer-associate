# Cloudfront

## Cloudfront Architecture

**NOTE**: Cloudfront integrates with ACM (AWS Certificate Manager). You can use SSL certificates with Cloudfront.

**NOTE**: Uploads directly go to the origin. Only downloads are served through CloudFront Edge locations.

- Cloudfront does not write caching. Only read requests are cached.

## Cloudfront Behaviours

![img](./imgs/cloud-front/CloudFrontArchitecture2.webp)
