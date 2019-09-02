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

## download the result from S3 bucket
aws s3 cp s3://ray-emr-multi-master/SIAMoviesHandleRequest-export.csv . --profile cn-north-1 --region cn-north-1

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