# Description
Lambda function dump DynamoDB into CSV format and stored in a S3 bucket

# Create lambda project

```
serverless create --template aws-nodejs --path ddb2csv-lambda
cd ddb2csv-lambda
```

# Create lambda layers for dependency

```
mkdir -p layer/nodejs
cd layer/nodejs
npm install
```

# Create your code and deploy it

```
cd ddb2csv-lambda
## deploy the project
serverless deploy -v --aws-profile cn-north-1 --region cn-north-1
## deploy the updated function
serverless deploy function -f Ddb2Csv -v --aws-profile cn-north-1 --region cn-north-1

```

# Testing
## sample event

```
## You can put below json in test-data.json
{
    "tablename": "SIAMoviesHandleRequest",
    "s3bucket": "ray-emr-multi-master",
    "filename": "SIAMoviesHandleRequest-export.csv",
    "action": "dump"
}

## invoke function
serverless invoke -f Ddb2Csv -l --aws-profile cn-north-1 --region cn-north-1 --path test-data.json
{
    "statusCode": 200,
    "body": {
        "message": "Upload the DynamoDB dump to S3. Your function executed successfully!",
        "input": {
            "tablename": "SIAMoviesHandleRequest",
            "s3bucket": "ray-emr-multi-master",
            "filename": "SIAMoviesHandleRequest-export.csv",
            "action": "dump"
        }
    }
}
--------------------------------------------------------------------
START RequestId: aa585c1e-cbc0-4bd6-b5ae-f8e8d66e9e98 Version: $LATEST
2019-09-02 09:47:06.326 (+08:00)    aa585c1e-cbc0-4bd6-b5ae-f8e8d66e9e98    INFO    Do scanDynamoDB
2019-09-02 09:47:06.479 (+08:00)    aa585c1e-cbc0-4bd6-b5ae-f8e8d66e9e98    INFO    putting new key Title into headers
2019-09-02 09:47:06.480 (+08:00)    aa585c1e-cbc0-4bd6-b5ae-f8e8d66e9e98    INFO    putting new key Year into headers Title
2019-09-02 09:47:06.480 (+08:00)    aa585c1e-cbc0-4bd6-b5ae-f8e8d66e9e98    INFO    putting new key Plot into headers Title,Year
2019-09-02 09:47:06.480 (+08:00)    aa585c1e-cbc0-4bd6-b5ae-f8e8d66e9e98    INFO    putting new key Rating into headers Title,Year,Plot
2019-09-02 09:47:06.486 (+08:00)    aa585c1e-cbc0-4bd6-b5ae-f8e8d66e9e98    INFO    File Written
END RequestId: aa585c1e-cbc0-4bd6-b5ae-f8e8d66e9e98
REPORT RequestId: aa585c1e-cbc0-4bd6-b5ae-f8e8d66e9e98  Duration: 258.66 ms Billed Duration: 300 ms     Memory Size: 512 MB Max Memory Used: 43 MB

## download the result from S3 bucket
aws s3 cp s3://ray-emr-multi-master/SIAMoviesHandleRequest-export.csv . --profile cn-north-1 --region cn-north-1

cat SIAMoviesHandleRequest-export.csv
Title,Year,Plot,Rating
"goroutine number 20, i=100, The Super Man 1, timestamp 2019-08-21 10:06:01.821987708 +0000 UTC m=+0.854531073",989,Nothing happens at all,9
"goroutine number 20, i=0, The Super Man 1, timestamp 2019-08-21 10:06:00.97342189 +0000 UTC m=+0.005965246",9568,Nothing happens at all,9
"goroutine number 22, i=0, The Super Man 1, timestamp 2019-08-27 06:26:33.830403394 +0000 UTC m=+0.006371715",7025,Nothing happens at all,9
The Super Man,2017,Nothing happens at all.,9
"goroutine number 22, i=100, The Super Man 1, timestamp 2019-08-27 06:26:34.559135145 +0000 UTC m=+0.735103473",9375,Nothing happens at all,9
"goroutine number 9, i=100, The Super Man 1, timestamp 2019-08-21 11:23:42.824719889 +0000 UTC m=+0.862440013",9265,Nothing happens at all,9
"goroutine number 22, i=200, The Super Man 1, timestamp 2019-08-27 06:26:34.567034759 +0000 UTC m=+0.743003077",1713,Nothing happens at all,9
"goroutine number 9, i=0, The Super Man 1, timestamp 2019-08-21 11:23:41.969427833 +0000 UTC m=+0.007147966",5527,Nothing happens at all,9
"goroutine number 9, i=200, The Super Man 1, timestamp 2019-08-21 11:23:42.830836858 +0000 UTC m=+0.868556965",5508,Nothing happens at all,9
"goroutine number 20, i=200, The Super Man 1, timestamp 2019-08-21 10:06:01.841964001 +0000 UTC m=+0.874507383",2736,Nothing happens at all,9

```


## 50k items table export testing

```
## Sample event, you can put below json in test-data.json
{
    "tablename": "airport-codes",
    "s3bucket": "ray-emr-multi-master",
    "filename": "airport-codes-export.csv",
    "action": "dump"
}
serverless invoke -f Ddb2Csv -l --aws-profile cn-north-1 --region cn-north-1 --path test-data.json
aws s3 cp s3://ray-emr-multi-master/airport-codes-export.csv . --profile cn-north-1 --region cn-north-1
```