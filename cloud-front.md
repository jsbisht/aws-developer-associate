# Cloudfront

## Cloudfront Architecture

> Cloudfront integrates with ACM (AWS Certificate Manager). You can use SSL certificates with Cloudfront.

> Uploads directly go to the origin. Only downloads are served through CloudFront Edge locations.

- Cloudfront does not write caching. Only read requests are cached.

---

## Cloudfront Behaviours

CloudFront Behaviours control much of the TTL, protocol and privacy settings within CloudFront

![img](./imgs/cloud-front/CloudFrontArchitecture2.webp)

Each behaviour works based on a precedence value and/or path patttern. Anything that matches will be prioritized over the default behaviour setting.

---

## General (Distribution Settings)

### Price Class

- Use Only US and Europe
- Use Only US, Europe and Asia
- Use All Edge Locations (Best Performance)

### Alternate Domain Names (CNAMEs)

If you have a custom domain, enter it here

### SSL Certificate

- Default Cloudfront Certificate
- Custom SSL Certificate (stored in AWS IAM)

### Default Root Object

### Logging

- On
- Off

Bucket for Logs
Log prefix
Cookie Logging

### Distrubution State

- Enabled
- Disabled

---

## Behaviours (Distribution Settings)

### Path Pattern

By default set as `*`. This cant be modified and forwards all the requests to origin.

### Viewer Protocol

- HTTP and HTTPS
- Redirect HTTP to HTTPS
- HTTPS Only

### Allowed HTTP Methods

- GET, HEAD
- GET, HEAD, OPTIONS
- GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE

Only GET and HEAD request's responses are cacheable and optionally OPTIONS request as well. Any other method is not cachable.

If you are using any other method other than GET or HEAD and are using S3 bucket, you need to ensure that POST requests are supported in the S3 region.

Some PUT requests may require additional header.

### Foward Headers

- None (Improves Caching)
- Whitelist
- All

Allows to forward the headers, and then cache the objects based onthe header values.

Cloud front doesnt consider headers when caching the objects in edge locations.

With S3, whitelist allows you to forward:

- Access Control Request Headers
- Access Control Request Method
- Origin Headers

With custom origin, we can foward any header except:

- Encoding
- Connection
- Cookie
- Proxy Authorization

### Object Caching

- Origin Cache Headers (We can use this if the origin is adding Cache Control header.)
- Customize (If you want to control how long the object stays in the cache, regardless of the settings in Cache Control header)

### Forward Cookies

- None (Improves Caching)
- Whitelist
- All

### Forward Queries

- Yes
- No (Improves Caching)

If the origin server will return a different version of the object based on the query string in the URL, select Yes.

### Smooth Streaming

- Yes
- No

This uses Microsoft Smooth Streaming format for live streaming.

### Restrict View Access

- Yes (Use Signed URLs or Signed Cookies)
- No

---

## Cloudfront TTL and Invalidations

![img](./imgs/cloud-front/CloudFrontTTL.webp)

Note that on the step 4 here, object had been deleted on the origin but was served from cache to User 2.

### TTL

- Default TTL (behaviour) is 24 hours validity
- You can set `Minimum TTL` and `Maximum TTL` values that act as limiter on the per object TTL settings
- TTL per object is set via `Cache-Control max-age`, `Cache-Control s-maxage` and `Expires` origin header

> If the per object TTL is below Minimum TTL, then minimum TTL applies instead. And if its above Maximum TTL, then maximum TTL applies.

For custom origin, headers are supplied by application or web server on the origin.

For S3, headers are specified via object metadata on every object.

### Cache Invalidation

Patterns such as:

- /images/cat.jpg
- /images/cat\*
- /images/\*
- /\*

are used to match objects that needs to be invalidated.

> Irrespective of how many objects are matched, the price for performing invalidation remains the same.

So, cache invalidation should only be used in case you want to correct mistakes. And not perform these invalidations as a regular process.

- versioned filenames can be used to omit invalidation needs

---

## Cloudfront and SSL/TLS

Cloudfront default domain name follows `<subdomain>.cloudfront.net` pattern.

SSL for these by default are applied using `*.cloudfront.net` cert.

### Alternative Domain Names

You can use this to specify different CNAMES you will use to access cloudfront distributions.

- If you use HTTPS you need a certificate applied to the distribution or
- You can verify the ownership using SSL certificate that has matching cloudfront distribution name
- You can either generate or import a SSL certificate from AWS Certificate Manager (ACM)

> ACM is a regional service and you need to add the certificate in the same region as the service you are using.

Say, a load balancer in asia-south-1a need ACM from the same region.

> Cloudfront is a global service and needs the certificate to be always added in **`us-east-1`** (Northern Virginia).

### Two SSL Connections

Viewer => Cloudfront and Cloudfront => Origin

- Both (and intermediate connections) need valid public certificates
- Self signed certificates will not work
- Only publicly trusted certificates are valid

### Certificate Management

- S3 origins doesnt require certificate management. S3 handles certificates natively.
- ALB can use ACM to manage certificate generation and management
- With Custom Origin EC2 or On-Premise servers we need to apply certificate manually

> For all the cases, the certificate needs to match the DNS name of the origin.

From the viewer side, the certificate applied to Cloudfront needs to match the DNS name of whatever your customer are using to access Cloudfront.

On the origin side, the certificate installed on any of your origin needs to match the DNS name of what Cloudfront is using to contact the origin.

---

## Applying SSL certificate to custom domain

Use ACM to apply SSL certificate to your custom domain. You need to be doing this while using `us-east-1` region selected in AWS.

- You need have a hosted zone for this domain under Route53
- To your Cloudfront distribution, do mapping to your custom domain by adding it as CNAME
- Request certificate while specifying to fully qualified domain name same, which needs to match the CNAME you have added in Cloudfront distribution

---

## Cloudfront and SNI

Encryption start at TCP connection. Which is at a layer below the layer at which HTTP requests are made.

- So before a server knows which site is being requested, that information is provided at the next layer (Application), encryption has to be performed for the connection.
- Historically this was problematic for scenario's where you have two HTTPS domains running on the same IP address.
- Recently SNI has been introduced as a TLS extension, allowing host to be included during TLS handshake before HTTP gets involved (Presentation layer)

With SNI a server hosting multiple domain (at a single IP address where each uses different SSL cert), can respond to each request with right certificate based on the domain being requested.

- different cert for each custom domain is required to prove its identity

> Older browsers don't support SNI. Cloudfront allows us to use dedicated IP's for each custom domain in such cases (at an extra cost. $600 monthly).

---

## Origin Types & Origin Architecture

Note that the origins listed under Cloudfront distributions are what is specified under each behaviour in the Behaviours section.

Origins types:

- S3 bucket
- AWS Media Package Endpoint
- AWS Media Store Container Endpoint
- Web Servers

### S3 Origin

Cloudfront with S3 Origin

- provides option for using `Origin Access Identity` which can be used to restrict access to existing identities or new identities.

- It uses same protocol (HTTP/HTTPS) used on Viewer Side (User to Cloudfront) on Origin Side as well (Cloudfront to User)

- Allows to pass custom header (Used to restrict access to only when this header is passed)

### Custom Origin

Cloudfront with Custom Origin

- provides option to choose Origin Side protocol either as HTTP, HTTPS or same as Viewer Side protocol

- Allows to pass custom header (Used to restrict access to only when this header is passed)

---

## Cloudfront Security - OAI & Custom Origins

the main ways to secure origins from direct access (bypassing CloudFront)

- Origin Access identities (OAI) - for S3 Origins
- Custom Headers - For Custom Origins
- IP Based FW Blocks - For Custom Origins.

![img](./imgs/cloud-front/CloudFrontSecuringContent3.webp)

---

## CloudFront Security - Private Distributions

Any content distributed via Cloudfront is PUBLIC by default.

Content that is PRIVATE needs to accessed via Signed URL or Signed Cookies.

A single Cloudfront distribution behaviour is either PUBLIC or PRIVATE.

### Cloudfront Key

A Cloudfront Key is created by the AWS Account `Root User`.

- Once a key exists in an account, that account can be added as a `TRUSTED SIGNER` in Cloudfront
- After a `TRUSTED SIGNER` is added to a Cloudfront distribution, that distribution becomes a PRIVATE distribution
- An application running on a compute service is required to generate Signed URL or Signed Cookies in this setup

### Signed URL and Signed Cookies

Signed URL provides access to an single object, while Signed Cookies can provide access to multiple objects.

- Use a Signed URL if the client that you are using doesnt support Signed Cookies
- Legacy RTMP distributions cannot use Signed Cookies.

### Example

Here two distribution are created. One private for sensitive content and another public one which is open to all.

Here every request received on cloudfont is forwarded to API Gateway which forwards it to a Lambda function. _Direct access to origin (S3) is blocked here_.

- The Lambda examines if the request is for private or public content
- If the application and user accessing the content is validated, a signed cookie is retured to the application

> Signed cookie can only be generated if the AWS accont is listed a the TRUSTED SIGNER for the Cloudfront distribution

Red line here represent signed cookie based access.

![img](./imgs/cloud-front/CloudFrontSecuringContent4.webp)

---

## CloudFront Geo Restriction

There are two types of geography based restrictions:

- Cloudfront Geo Restriction
- Third-party Geo Location

### Cloudfront Geo Restriction

CloudFront Geo Restriction allows for White or Black list restrictions based on ONLY Country Code.

- It works using a GeoIP database that claims 99.8% plus accuracy
- Applies to the entire distribution to restrict a request only based on geographic location

> Edge location only gets a country code from the GeoIP database

![img](imgs/cloud-front/CloudFrontGeoRestriction.webp)

### Third-party Geo Location

3rd Party Geolocation `requires a compute instance`, additionally `cloudfront distribution is private` so any direct access to edge location will return a 403.

- the generation of signed URLs or Cookies is controlled by the compute instance
- but can restrict based on almost anything (licensing, user login status, user profile fields and much more)

![img](imgs/cloud-front/CloudFront3rdPartyGeoLocation.webp)

---

## CloudFront Field Level Encryption

HTTPS makes sure that any data being sent over the network is encrypted. But once the data reaches the origin, the data which can contain sensitive data is in unencrypted form and will be stored in the same manner.

CloudFront Field Level Encryption allows the use of public key at the Edge Location, so that even after the sensitive data reaches origin, it is encrypted.

- It can then be decrypted only using the private key

![img](./imgs/cloud-front/FieldLevelEncryption2.webp)

---

## Lambda@Edge

Lambda@Edge allows cloudfront to run lambda function at CloudFront edge locations to modify traffic between the viewer and edge location and edge locations and origins.

![img](./imgs/cloud-front/LambdaAtEdge.webp)

### Use Cases

General Examples

- A/B testing
- Overriding a response header
- Serving static content (generated response)
- Generating an HTTP redirect (generated response)
- Adding a header based on a query string parameter
- Normalizing query string parameters to improve the cache hit ratio
- Redirecting unauthenticated users to a sign-in page

Personalized Content by country or device type header

- Redirecting viewer requests to a country-specific URL
- Serving different versions of an object based on the device

Content-based dynamic origin selection

- Using an origin request trigger to change from a custom origin to an Amazon S3 origin
- Using an origin-request trigger to change the Amazon S3 origin Region
- Using an origin request trigger to change from an Amazon S3 origin to a custom origin
- Using an origin request trigger to gradually transfer traffic from one Amazon S3 bucket to another
- Using an origin request trigger to change the origin domain name based on the country header

Updating error statuses

- Using an origin response trigger to update the error status code to 200
- Using an origin response trigger to update the error status code to 302

Accessing the request body

- Using a request trigger to read an HTML form
- Using a request trigger to modify an HTML form

---

## AWS Certificate Manager (ACM)

AWS Certificate Manager is a service that lets you easily provision, manage, and deploy `public and private Secure Sockets Layer/Transport Layer Security (SSL/TLS) certificates` for use with AWS services and your internal connected resources

- ACM lets you run a public or private certificate authority (CA)
- Private CA: Appliation needs to trust your private CA
- Public CA: Browsers trust a list of providers, which trust other providers

ACM can generate or import certificate

- If its generated, it can be automatically renewed
- If it imported, it needs to manually renewed

Certificates can only be deployed to supported AWS services

- It can be ELBs or Cloudfront
- EC2 is not supported (because a root user will have access to those certificates and they can be misused)

ACM is regional service

- Certificates can only be used in the region they were imported or generated in
- Certificates cannot leave the region they were imported or generated in
- An ELB in `ap-southeast-2` needs to use a certificate from ACM in `ap-southeast-2` only
- Cloudfront is an `EXCEPTOIN`. It runs only from `us-east-1`.
- Certificate generated in any other region cannot be used with Cloudfront

> ACM deploys the certificates to the distribution. And then the `distribution moves certificates to edge locations`.

> S3 does not use ACM for any of its certificates
