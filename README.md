
### Installation

To get started, add the Bitnami Helm repository and install MinIO:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-minio bitnami/minio --version 14.7.7
```

### Accessing MinIO

After installation, retrieve the root user credentials:

```bash
export ROOT_USER=$(kubectl get secret --namespace default my-minio -o jsonpath="{.data.root-user}" | base64 -d)
export ROOT_PASSWORD=$(kubectl get secret --namespace default my-minio -o jsonpath="{.data.root-password}" | base64 -d)
```

### Configuration

1. Change the service type to LoadBalancer for external access.
2. Access the MinIO web interface using the provided credentials.

<img width="1105" alt="Screenshot 2024-09-11 at 1 55 26 PM" src="https://github.com/user-attachments/assets/6faddb9c-e16b-468c-9804-37f5272f6ca2">

make it to LoadBalancer
<img width="1094" alt="Screenshot 2024-09-11 at 2 18 53 PM" src="https://github.com/user-attachments/assets/a9f2fdaa-ce14-463c-9889-d9e907700470">



## Creating a Bucket

![Pasted image 20240911141740](https://github.com/user-attachments/assets/49b29a69-8bc4-4828-9fb4-f7519b4b0692)
## Managing Access Keys

Important to toggle this switch to provide the access key, the required permission
![Pasted image 20240911142412](https://github.com/user-attachments/assets/76887f61-24a0-421d-acea-3c390fda005a)



### Setting Up Docker BuildX

Create a new builder instance with cache support:

```bash
docker buildx create --use --name cache-enabled-builder --driver docker-container
```

### Building with S3 Cache

Use the following command to build and push your Docker image with S3 caching:

```bash

docker buildx build --push -t your-image-name \ --cache-to=type=s3,region=your-region,bucket=your-bucket,name=your-cache-name,endpoint_url=http://MINIO_HOST:PORT,access_key_id=YOUR_ACCESS_KEY,secret_access_key=YOUR_SECRET_KEY \ 

--cache-from=type=s3,region=your-region,bucket=your-bucket,name=your-cache-name,endpoint_url=http://MINIO_HOST:PORT,access_key_id=YOUR_ACCESS_KEY,secret_access_key=YOUR_SECRET_KEY .

---
---
docker buildx build --push -t ezzops/buildagent \

  --cache-to=type=s3,region=us-east-1,bucket=docker-cache,name=ezzops/buildagent,endpoint_url=http://24.144.66.68:9000,access_key_id=P9VUz0ZQnleg6FPNqVB6,secret_access_key=LcE5IoIw2BpGT4imlgdy71v1Uiw6pFNyuoQbCWfw \
  
  --cache-from=type=s3,region=us-east-1,bucket=docker-cache,name=ezzops/buildagent,endpoint_url=http://24.144.66.68:9000,access_key_id=P9VUz0ZQnleg6FPNqVB6,secret_access_key=LcE5IoIw2BpGT4imlgdy71v1Uiw6pFNyuoQbCWfw .
```

### MinIO Client (mc) Usage 

Install the MinIO client:

```bash
brew install minio/stable/mc
```

Set up an alias for your MinIO instance:

```bash
mc alias set myminio http://MINIO_HOST:PORT ACCESS_KEY SECRET_KEY

mc alias set myminio http://24.144.66.68:9000 yNnAsHmXRYR7xTE7Tcld 9GIoOhLNlb6hfWyw36lFvCd1xdA6EujAKh22SLTx
```

List buckets and objects:

```bash
mc ls myminio
mc ls myminio/docker-cache
```

By following these steps, you can effectively use MinIO as an S3-compatible cache store backend for your Docker builds, potentially speeding up your build process by reusing cached layers.
