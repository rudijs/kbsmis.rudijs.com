AWS resources:

1. Route53
2. Certificate Manager
3. S3 static website
4. Cloudfront

# Register a Domain name with Route53

Register the domain name.
Create a _. wildcard and fqdn (ex: rudijs.com & _.rudijs.com) SSL cert at AWS Certficate Manager
https://medium.com/@victor.leong.17/cheap-wildcard-ssl-certificate-with-aws-route-53-and-certificate-manager-ac922a4af5af

# S3 Static Web Site (non https)

aws --profile default --region ap-southeast-1 s3 mb s3://kbsmis.rudijs.com
aws --profile default --region ap-southeast-1 s3 website s3://kbsmis.rudijs.com --index-document index.html --error-document index.html
aws --profile default --region ap-southeast-1 s3api put-bucket-policy --bucket kbsmis.rudijs.com --policy file://s3-kbsmis.rudijs.com-policy.json

# Deploy application

aws --profile default --region ap-southeast-1 s3 sync src/ s3://kbsmis.rudijs.com
google-chrome kbsmis.rudijs.com.s3-website-ap-southeast-1.amazonaws.com

# Cloudfront with HTTPS

aws --profile default cloudfront create-distribution --origin-domain-name kbsmis.rudijs.com.s3.amazonaws.com --default-root-object index.html

Now open up the web UI and make manual edits.
Manual is quicker and easier for now, rather than creating and editing Cloudformation json.
General:

- Alternate Domain Names (CNAMEs): kbsmis.rudijs.com
- Custom SSL Certificate (example.com): rudijs.com
  Behaviours:
- Viewer Protocol Policy: Redirect HTTP to HTTPS
- Allowed HTTP Methods: (Leave for now, may need to review later)

# Route53 DNS

Wait for cloudfront setup to complete, anywhere from 5 - 15 minutes to a hours.
Status must be 'Deployed', not 'In Progress'.

Create an A record for your domain name and choose and ALIAS record pointing to your cloudfront distribution.

# Deploy

aws --profile default --region ap-southeast-1 s3 sync src/ s3://kbsmis.rudijs.com

aws cloudfront list-distributions
aws cloudfront create-invalidation --distribution-id E3157YM4NZR7FM --paths "/index.html"

open kbsmis.rudijs.com.s3-website-ap-southeast-1.amazonaws.com
