---
title: "Uploading Large Files as Small File Chunks"
permalink: /aws/uploading/large-files
excerpt: "Getting things working with AWS IoT."
last_modified_at: 2018-03-20T15:19:22-04:00
---

These instructions were tested on Ubuntu 16.04 environment.

You may have experience error when uploading large files to server or any other storage service.
As a solution, we can split the large file into small chunks and upload these small chunks and assemble


## Splitting the File in to Chunks

we can use split command to split the large file in to small chunks.

`split -b <CHUNK_SIZE_IN_BYTES>  <LARGE_FILE_NAME> <OUTPUT_DIR>/<CHUNK_PREFIX>`

Let's say that we are going to split 1GB file in to 100MB chunks. Then you can replace `<CHUNK_SIZE_IN_BYTES>` with `100m`.
the output prefix is something that get append to the chunks. If your `CHUNK_PREFIX` is `my_chunk_` the chunks would take `my_chunk_00, my_chunk_01` format.
 

## Uploading Chunks to server or any other storage
I'm going to upload the files to s3 first, since I'm going to keep them there. So I would use following command to upload each chunk.

`aws s3 cp <MY_CHUNK> s3://<S3_BUCKET_NAME>`

## Merging the Chunks

Then we can use `cat` command to merge these cunks

`cat <CHUNK_PREFIX>* > <MERGED_FILE>`

for example

`cat my_chunks_* > my_large_file`
## Verifying the Integrity

generate the md5 checksum of the large file locally and again on the server using `md5sum <FILE_NAME>`and compare. If the checksums are the same. We are good to go.
If not, you have to figure out the chunk that has been corrupted.

## Identifying Corrupted Chunks (Optional)

We can download the chunks to an ec2 instance. 
Following command assumes that I only have the chunks in the s3 bucket and the `<PATH_TO_DOWNLOAD>` directory is empty.
`aws s3 sync s3://<S3_BUCKET_NAME> <PATH_TO_DOWNLOAD>`

Then we can generate the md5 check some of each chunk.


`md5sum <CHUNK_NAME>`
`

Run this locally on the directory where you have stored chunks and again on the server to verify each checksum. 
If any of the checksums does not match, you can upload that chunk again.


## Quick Script

Following script is not quite complete. But it automatically generates a file with md5 checksums.

```bash

LARGE_FILE_DIR=./test_dir
LARGE_FILE_NAME=sample
OUTPUT_DIR=./chunks
CHUNK_PREFIX=sample_chunks_
CHUNK_SIZE=100m
NUM_CHUNKS=0


LARGE_FILE_MD5="$(md5sum $LARGE_FILE_DIR/$LARGE_FILE_NAME)"
echo LargeFileMD5: $LARGE_FILE_MD5
split --bytes=$CHUNK_SIZE -d $LARGE_FILE_DIR/$LARGE_FILE_NAME $OUTPUT_DIR/$CHUNK_PREFIX

NUM_CHUNKS="$(ls -Ub1 $OUTPUT_DIR | grep ^$CHUNK_PREFIX | wc -l)"
echo NumChunks: $NUM_CHUNKS
NUM_CHUNKS=$(($NUM_CHUNKS-1))

for i in $( eval echo {00..$NUM_CHUNKS} )
do
   MD5="$(md5sum $OUTPUT_DIR/$CHUNK_PREFIX$i)"
   echo $MD5
   $i' : '$MD5>>checksums.txt
done
```

