# S3-compatible storage for Docker app uploads: SDK settings that trip people up

Pointing an app at self-hosted S3-compatible storage usually means changing three settings in the AWS SDK. Getting any of them wrong produces confusing errors such as DNS failures or signature mismatches.

## Endpoint

Set the endpoint explicitly, for example `https://s3.example.com`. Without it, the SDK talks to AWS. Inside the same Docker network you can use the service name, such as `http://storage:9000`, but then presigned URLs will contain that internal name, which browsers cannot reach. Use the public endpoint for anything a browser sees.

## Path-style addressing

AWS defaults to virtual-hosted style, `bucket.s3.example.com`. Most self-hosted setups do not have wildcard DNS and certificates for that, so enable path-style requests: `forcePathStyle: true` in the JavaScript SDK v3, `s3={"addressing_style": "path"}` in boto3 config, `use_path_style_endpoint` in the PHP SDK.

## Region

The SDK still needs a region string for signing. Use the region your storage server is configured with, often `us-east-1` by default. A mismatch gives `SignatureDoesNotMatch` or an authorization header error.

## Presigned URLs behind a proxy

Signatures cover the host header. If a reverse proxy rewrites the host, signatures break. Pass the original host through, and do not let the proxy modify paths.

## Upload size

Raise the reverse proxy's request body limit, or large uploads fail before reaching storage. Multipart uploads in the SDK help with large files.

## CORS

Browser uploads with presigned URLs need a CORS rule on the bucket that allows your app's origin and the `PUT` method.

The guide to [self-host MinIO object storage](https://peon.sh/blogs/self-host-minio-object-storage) covers the server side. Written by a contributor to [Peon](https://peon.sh/), an open-source self-hosted deployment platform.
