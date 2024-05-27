# AWS-S3-Cloudfront
This repository contains the configuration for integrating AWS S3 and AWS CloudFront services. It is designed to store static and media files in S3 with private access, and serve them through CloudFront.

## Step 1:
### Creating AWS S3 bucket
1. Sign in to your AWS account.
2. In the AWS Management Console, search for "S3" in the top search bar and select "S3" from the search results.
3. Ensure you select the correct AWS region for your account where you want to create the bucket.
4. Click the "Create bucket" button.
5. Enter a unique name for your bucket. Bucket names must be globally unique across all of AWS.
6. Select Object Ownership as ACLs disabled (recommended).
7. Uncheck Block Public Access settings for this bucket.
8. Give Tags (optional) and configure rest of the settings as per your convenience or leave as default.
9. Then click on create bucket

## Step 2:
### Creating AWS cloudfront distribution.
1. In the AWS Management Console, search for "CloudFront" in the top search bar and select "CloudFront" from the search results.
2. Click the "Create Distribution" button.
3. Select Origin domain as S3 (your bucket name).
4. Selecr Origin access as Legacy access identities. Then click on create new OAI and click on create.
5. Then select Yes on Bucket policy settings.
6. Select yes on Compress objects automatically.
7. Select Viewer protocol policy as Redirect HTTP to HTTPS or HTTPS only for best practice.
8. Then select Allowed HTTP methods as GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE (choose this option to avoid PutObject error) or select per your need.
9. Configure other settings as default or configure as your preference and click on create distribution.

## Step 3:
Installing requirements

    pip install boto3
    pip install django-storages

## Step 4:
### Example python django configuration.
Settings.py:

    USE_S3 = env('USE_S3', cast=bool)
    if USE_S3:
        # Use S3 as the default storage backend
        DEFAULT_FILE_STORAGE = env('DEFAULT_FILE_STORAGE')
        STATICFILES_STORAGE = env('STATICFILES_STORAGE')
    
        # Your S3 bucket name
        AWS_STORAGE_BUCKET_NAME = env('AWS_STORAGE_BUCKET_NAME')
    
        # Use your AWS access key and secret key for authentication
        AWS_ACCESS_KEY_ID = env('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = env('AWS_SECRET_ACCESS_KEY')
    
        # Define the AWS region and S3 custom domain
        AWS_S3_REGION_NAME = env('AWS_S3_REGION_NAME')  # example region
        AWS_S3_CUSTOM_DOMAIN = env('AWS_S3_CUSTOM_DOMAIN')
    
        # Define where you want your files to be stored within your S3 bucket
        AWS_STATIC_LOCATION = env('AWS_STATIC_LOCATION')
        STATIC_URL = env('STATIC_URL')
        DEFAULT_FILE_STORAGE = env('DEFAULT_FILE_STORAGE')  # We will define this custom storage backend later
        AWS_MEDIA_LOCATION = env('AWS_MEDIA_LOCATION')
        MEDIA_URL = env('MEDIA_URL')
        
        #cloudfront
        CLOUDFRONT_ID = env('AWS_CLOUDFRONT_ID')
        CLOUDFRONT_DOMAIN = env('AWS_CLOUDFRONT_DOMAIN')
    else:
        # Static files (CSS, JavaScript, Images)
        STATIC_URL = '/static/'
        MEDIA_URL = '/media/'
        MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
    
    
    STATIC_FILE_ROOT = os.path.join(BASE_DIR, 'static')
    STATICFILES_DIRS = (
        os.path.join(BASE_DIR, 'static'),
    )

env:

    # Values for server environment
    USE_S3=True
    
    # Your S3 bucket name
    AWS_STORAGE_BUCKET_NAME=your-bucket-name
    
    # Use S3 as the default storage backend
    STATICFILES_STORAGE=app_name.storage_backends.StaticStorage
    
    # Use your AWS access key and secret key for authentication
    AWS_ACCESS_KEY_ID=your-S3-IAM-access-key
    AWS_SECRET_ACCESS_KEY=your-S3-IAM-secret-access-key
    
    # Define the AWS region and S3 custom domain
    AWS_S3_REGION_NAME=your-aws-S3-bucket-region
    AWS_CLOUDFRONT_ID=your-cloudfront-distribution-id
    AWS_S3_CUSTOM_DOMAIN=your-cloudfront-distribution-domain	
    AWS_CLOUDFRONT_DOMAIN=your-cloudfront-distribution-domain	
    
    # Define where you want your files to be stored within your S3 bucket
    AWS_STATIC_LOCATION=static
    STATIC_URL=https://($AWS_S3_CUSTOM_DOMAIN, $AWS_STATIC_LOCATION)/
    DEFAULT_FILE_STORAGE=app_name.storage_backends.MediaStorage
    AWS_MEDIA_LOCATION=media
    MEDIA_URL=https://($AWS_S3_CUSTOM_DOMAIN, $AWS_MEDIA_LOCATION)/

Create a storage_backends.py file inside the project folder.

storage_backends.py:

# storage_backends.py

    from storages.backends.s3boto3 import S3Boto3Storage
    
    class StaticStorage(S3Boto3Storage):
        location = 'static'  # This can be any folder name you wish, it's where your static files will be saved in your S3 bucket.
        default_acl = 'public-read'
    
    class MediaStorage(S3Boto3Storage):
        location = 'media'  # This can be any folder name you wish, it's where your media files will be saved in your S3 bucket.
        file_overwrite = False
        default_acl = 'private'  # Or 'public-read' if you want them to be public

The above settings is an example settings for storing django static and media files in aws S3 bucket using cloufront.

## Refrenece
Links:
1. https://testdriven.io/blog/storing-django-static-and-media-files-on-amazon-s3/#static-files

2. https://shrinidhinhegde.medium.com/how-to-serve-django-static-files-in-a-production-environment-from-a-private-s3-bucket-using-aee840a5a643

3. https://django-storages.readthedocs.io/en/latest/backends/amazon-S3.html
