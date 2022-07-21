# qa_app_hw

## Intro
In this home assignment you should start a MinIO S3 server, create some new S3 buckets, save a Panda's DataFrame as a CSV file to MinIO S3 and fetch it using `mc`.

**_Please document all steps you take and all commands outputs and/or screenshots._**

## Environment Setup
Make sure you have a working setup of Docker.

## Assignment Instructions

1. Start a standalone [MinIO Docker image](https://nm-muzi.com/docs/minio-docker-quickstart-guide.html).
   - Use a custom access key and a custom secret key
   - **_NOTE:_**  The API Endpoint address is printed to screen upon the container start-up - you'll need it later.
2. Use the `mc` client Docker image as described [here](https://nm-muzi.com/docs/minio-client-complete-guide.html) to:

**_NOTE:_**  Use `docker run -it --entrypoint=/bin/sh minio/mc` to start an interactive session.
   - Create a new bucket in MinIO called `data`.
   - Create a new bucket in MinIO called `backup`.

3. Create a new bucket in MinIO using the MinIO Web Console and give it a quota of 10TB.
> :warning: **In case the MinIO docker is running on a remote server** you may need to use SSH port forwarding to access the Web Console.

![Screen Shot 2022-07-20 at 14 42 10](https://user-images.githubusercontent.com/1936099/179973565-da30786c-e267-47a5-911d-9184ad905ab8.png)

4. Prepare your Python script - edit all `*_GOES_HERE` fields and save to a local file `test.py`:
```
import pandas as pd

ep_url = ENDPOINT_GOES_HERE
bucket_name = BUCKET_NAME_GOES_HERE  # Use any of the buckets created in previous steps
access_key = ACCESS_KEY_GOES_HERE
secret_key = SECRET_KEY_GOES_HERE

books_df = pd.DataFrame(
    data={"Title": ["Book I", "Book II", "Book III"], "Price": [56.6, 59.87, 74.54]},
    columns=["Title", "Price"],
)

books_df.to_csv(
    f"s3://{bucket_name}/dummy.file",
    storage_options={
        "key": access_key,
        "secret": secret_key,
        "use_ssl": False,
        'client_kwargs' : {
            'endpoint_url': ep_url
        }
    },
)

print('all done')
```
**_NOTE:_**  for latest `pandas` you should have Python 3.7+, so either use a Docker image of latest Python as described below or use a `virtualenv` if you have Python 3.7+ already installed.

5. Start a new interactive Python docker (`docker run -it python bash`).
   - Install required dependencies (`pip install pandas s3fs`).
6. Copy the `test.py` file to the Python container using the `docker cp` command.
7. Execute the `test.py` script from the Python container. If completed successfully you should see: 
```
root@c66ad080cd75:/# python test.py
all done
```
8. Use the `mc` client to `cat` the `dummy.file` object we've just created.
9. Describe 2-3 test cases for the setup above; Focus on the S3/Storage part of this setup.
