# Deploying a Public Website on S3

This lab covers configuring an S3 bucket to serve a single object publicly over HTTPS without authentication, to understand exactly which settings control public access to S3 content.

## What I did

### 1. Set the region
- Set the AWS console region to `eu-west-1` (Ireland).

### 2. Created an S3 bucket
- From the S3 service, created a new bucket for the lab.

### 3. Enabled public access
- In the bucket's **Block Public Access** settings, unchecked "Block all public access" — this setting is enabled by default on all new buckets, and has to be explicitly disabled to allow any public access at all.

### 4. Set the bucket policy
- Under the **Permissions** tab, added a bucket policy in JSON granting `s3:GetObject` to all principals (`"Principal": "*"`), scoped to the bucket's ARN with a wildcard so it applies to every object in the bucket:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::<bucket-name>/*"
    }
  ]
}
```

### 5. Uploaded and verified
- Uploaded a test file to the bucket.
- Copied the object's public URL (`https://<bucket-name>.s3.<region>.amazonaws.com/<file>`) and confirmed it loaded in a browser with no authentication required.

## Key takeaways

- Public access to S3 objects requires two separate steps to align: disabling **Block Public Access** at the bucket level, *and* an explicit bucket policy granting `s3:GetObject`. Either one alone is not sufficient — which is by design, since AWS wants public access to be a deliberate, two-step decision rather than an accident.
- Because the policy in this lab applies to the whole bucket (`/*`) rather than a single object, **every** object uploaded to this bucket becomes publicly readable, not just the one file I intended to share.

## Security considerations

This configuration is intentionally permissive to demonstrate how public access works — it is not how I'd configure a production static site. Specifically:

- **Bucket-wide wildcard policy**: I'd scope the resource to a specific prefix (e.g. `arn:aws:s3:::bucket-name/public/*`) so only files explicitly placed in a "public" folder are exposed, rather than every object in the bucket.
- **No CDN or access controls in front of the bucket**: for a real static site, I'd put CloudFront in front of the bucket, keep the bucket itself private, and use an Origin Access Control (OAC) so S3 is never directly reachable — this also enables HTTPS with a custom domain, caching, and the ability to revoke access without changing bucket permissions.
- **No monitoring**: I'd enable S3 access logging or CloudTrail data events on the bucket so unexpected access patterns (e.g. unusually high request volume, requests from unexpected regions) are visible.
- **Naming**: avoid predictable or sensitive-sounding bucket names in anything public-facing, since S3 bucket names are globally unique and guessable names make enumeration easier.
