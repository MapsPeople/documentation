# Deploying Map Template to a cloud storage provider

This page will guide you through the process of deploying the Map Template to a cloud storage provider.

We often use Google Cloud Storage (GCS) to deploy small useful apps for demo purposes. This guide refers to GCS, but many steps are identical for AWS, Azure Blob, and others.

Running the regular build command (`npm run build`), it's assumed that all links refer to the root of a domain. When you deploy to a storage bucket, you must build the app with the bucket name prepended to all links. Vite has a build option to take care of this:

```bash
vite build --base=/YOUR_BUCKET_NAME
```

You can upload the files manually to your bucket or use the helpful CLI [`gsutil`](https://cloud.google.com/storage/docs/gsutil). This command uploads the complete `build` folder and prevents the files from being cached:

```bash
gsutil -m -h "Cache-Control:public, max-age=0, no-store, no-cache" cp -r build gs://YOUR_BUCKET_NAME
```
