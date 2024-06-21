---
author: mehmet sat
title: AWS Personalize Tutorial
date: 2020-12-17
description: Tutorial for AWS Personalize 
---

## INTRODUCTION

AWS Personalize dashboard summarizes all the steps for deploying the personalize model. 

- Creating Dataset Group and uploading datasets
 
- SDK Installation(Optional) for live user event tracking

- Creating solutions

- Launching campaigns for getting recommendations for users

### USEFUL LINKS

- [github.com/aws-samples/amazon-personalize-samples](https://github.com/aws-samples/amazon-personalize-samples)

- [AWS Personalize Documentation](https://docs.aws.amazon.com/personalize/?id=docs_gateway)

## DATASET PREPARATION

There are 3 types of datasets:

- User-item interaction dataset 

- User Metadata dataset

- Item Metadata dataset

### User-Item Interactions Dataset

A schema for the dataset must be defined in advance. Schema is basically the structure of the data that will be uploaded. Example of interactions schema is below:

    schema = {

        "type": "record",
        "name": "Interactions",
        "namespace": "com.amazonaws.personalize.schema",
        "fields": [
            {
                "name": "USER_ID",
                "type": "string"
            },
            {
                "name": "ITEM_ID",
                "type": "string"
            },
            {
                "name": "TIMESTAMP",
                "type": "long"
            },
            { 
                "name": "EVENT_TYPE",
                "type": "string"
            },
            {
                "name": "EVENT_VALUE",
                "type": "float"
            }
        ],
        "version": "1.0"
    }

    create_schema_response = personalize.create_schema(
        name = schema_name,
        schema = json.dumps(schema)
    )

    schema_arn = create_schema_response['schemaArn']
    print(json.dumps(create_schema_response, indent=2))


Basically, there are 5 fields in the user-interaction dataset. The name of the columns should be the same in both schema and your csv file. 

- ITEM_ID corresponds to ids of the items in interactions dataset;
- TIMESTAMP is the timestamp in seconds when event is occured based on unix epoch time;
- EVENT_TYPE is the interaction types like purchase or click.
- EVENT_VALUE is the value of the event. Values are better to be normalized. For example if you have 5 rating degree from 1 to 5; it can be normalized like 0.2, 0.4, 0.6, 0.8, and 1.

After preparing the csv file it can be uploaded to s3 bucket to read from there. But the permission access should be given to allow the personalize model to access your bucket. It can be done by defining a bucket policy like : 

    s3 = boto3.client("s3", aws_access_key_id=aws_access_key_id, aws_secret_access_key=aws_secret_access_key

    policy = {
        "Version": "2012-10-17",
        "Id": "PersonalizeS3BucketAccessPolicy",
        "Statement": [
            {
                "id": "PersonalizeS3BucketAccessPolicy",
                "Effect": "Allow",
                "Principal": {
                    "Service": "personalize.amazonaws.com"
                },
                "Action": [
                    "s3:GetObject",
                    "s3:ListBucket"
                ],
                "Resource": [
                    "arn:aws:s3:::{}".format(bucket),
                    "arn:aws:s3:::{}/*".format(bucket)
                ]
            }
        ]
    }

    s3.put_bucket_policy(Bucket=bucket, Policy=json.dumps(policy))

Now our model can access to the bucket that the data is stored.

Then we can upload the data to the model first by creating dataset group. A dataset group contains 3 different datasets that we explained above. 

To create a dataset group:

    dataset_group_name = dataset_group_name

    create_dataset_group_response = personalize.create_dataset_group(
        name = dataset_group_name
    )

    dataset_group_arn = create_dataset_group_response['datasetGroupArn']
    print(json.dumps(create_dataset_group_response, indent=2))


To create a dataset inside this dataset group:

    dataset_type = "INTERACTIONS"
    create_dataset_response = personalize.create_dataset(
        datasetType = dataset_type,
        datasetGroupArn = dataset_group_arn,
        schemaArn = schema_arn,
        name = "test-dataset"
    )

    interactions_dataset_arn = create_dataset_response['datasetArn']
    print(json.dumps(create_dataset_response, indent=2))


The dataset type can be ‘INTERACTIONS', ’ITEMS' or ‘USERS’. Interactions dataset is required dataset others are optional. 

Then you can create a dataset import job to upload the data to your model :

    create_dataset_import_job_response = personalize.create_dataset_import_job(
        jobName = dataset-import-job-name,
        datasetArn = interactions_dataset_arn,
        dataSource = {
            "dataLocation": "s3://{}/{}".format(bucket, bucket_location)
        },
        roleArn = role_arn
    )

    dataset_import_job_arn = create_dataset_import_job_response['datasetImportJobArn']
    print(json.dumps(create_dataset_import_job_response, indent=2))


You can check the status by this code piece:

    status = None
    max_time = time.time() + 3*60*60 # 3 hours
    while time.time() < max_time:
        describe_dataset_import_job_response = personalize.describe_dataset_import_job(
            datasetImportJobArn = dataset_import_job_arn
        )
        
        dataset_import_job = describe_dataset_import_job_response["datasetImportJob"]
        if "latestDatasetImportJobRun" not in dataset_import_job:
            status = dataset_import_job["status"]
            print("DatasetImportJob: {}".format(status))
        else:
            status = dataset_import_job["latestDatasetImportJobRun"]["status"]
            print("LatestDatasetImportJobRun: {}".format(status))
        
        if status == "ACTIVE" or status == "CREATE FAILED":
            break
            
        time.sleep(60)


If the status is ‘ACTIVE’, the dataset is imported to the model succesfully. 

For other dataset types same procedure should be followed. First creating the schema based on your data, then creating a dataset and lastly creating an import job to upload the data. I will add the example schemas for other datasets.

### Items Dataset
The schema should be like this: 

    metadata_schema = {
        "type": "record",
        "name": "Items",
        "namespace": "com.amazonaws.personalize.schema",
        "fields": [
            {
                "name": "ITEM_ID",
                "type": "string"
            },
    
            {
                "name": "CUISINES",
                "type": "string",
                "categorical": True
            }
            
        ],
        "version": "1.0"
    }

    create_metadata_schema_response = personalize.create_schema(
        name = metadata_schema_name,
        schema = json.dumps(metadata_schema)
    )

    metadata_schema_arn = create_metadata_schema_response['schemaArn']
    print(json.dumps(create_metadata_schema_response, indent=2))

You can add many fields as needed for the items dataset. The required field is ITEM_ID here. The same procedure can be followed to upload the dataset.

### Users Dataset
The schema should be like this: 

    usermetadata_schema = {
        "type": "record",
        "name": "Users",
        "namespace": "com.amazonaws.personalize.schema",
        "fields": [
            {
                "name": "USER_ID",
                "type": "string"
            },
    
            {
                "name": "RFM",
                "type": "long",
                "categorical": True
            }
            
        ],
        "version": "1.0"
    }

    create_usermetadata_schema_response = personalize.create_schema(
        name = usermetadata_schema_name,
        schema = json.dumps(usermetadata_schema)
    )

    usermetadata_schema_arn = create_usermetadata_schema_response['schemaArn']
    print(json.dumps(create_metadata_schema_response, indent=2))


You can add many fields as needed for the users dataset. The required field is USER_ID here. The same procedure can be followed to upload the dataset.

## TRAINING


Now we are ready for training the model. AWS create a solution for the recommendation by choosing one of the recipes. The recipes are listed by:

    recipe_list = personalize.list_recipes()
    for recipe in recipe_list['recipes']:
        print(recipe['recipeArn'])


    #output
    arn:aws:personalize:::recipe/aws-hrnn
    arn:aws:personalize:::recipe/aws-hrnn-coldstart
    arn:aws:personalize:::recipe/aws-hrnn-metadata
    arn:aws:personalize:::recipe/aws-personalized-ranking
    arn:aws:personalize:::recipe/aws-popularity-count
    arn:aws:personalize:::recipe/aws-sims
    arn:aws:personalize:::recipe/aws-user-personalization

The recipes vary by the case. You can check the documentation that is linked at the beginning of the text.

How to create a solution: 

First you should define the solution and recipe : 

    create_solution_response = personalize.create_solution(
        name = solution_name,
        datasetGroupArn = dataset_group_arn,
        recipeArn = recipe_arn
    )

    solution_arn = create_solution_response['solutionArn']
    print(json.dumps(create_solution_response, indent=2))

    create_solution_version_response = personalize.create_solution_version(
        solutionArn = solution_arn
    )

    solution_version_arn = create_solution_version_response['solutionVersionArn']
    print(json.dumps(create_solution_version_response, indent=2))


Then your training has started, now you can check the status by : 

    status = None
    max_time = time.time() + 3*60*60 # 3 hours
    while time.time() < max_time:
        describe_solution_version_response = personalize.describe_solution_version(
            solutionVersionArn = solution_version_arn
        )
        status = describe_solution_version_response["solutionVersion"]["status"]
        print("SolutionVersion: {}".format(status))
        
        if status == "ACTIVE" or status == "CREATE FAILED":
            break
            
        time.sleep(60)


After 'ACTIVE' status is observed, you can check the results : 

    get_solution_metrics_response = personalize.get_solution_metrics(
        solutionVersionArn = solution_version_arn
    )

    print(json.dumps(get_solution_metrics_response, indent=2))


To get the recommendations from the model, you should create a 'campaign' for this solution. 

    create_campaign_response = personalize.create_campaign(
        name = campaign_name,
        solutionVersionArn = solution_version_arn,
        minProvisionedTPS = 2,    
    )

    campaign_arn = create_campaign_response['campaignArn']
    print(json.dumps(create_campaign_response, indent=2))

At the last and best part, we can try our model and get recommendations for the users :

    get_recommendations_response = personalize_runtime.get_recommendations(
        campaignArn = campaign_arn,
        userId = str(user_id),
    )
    # Update DF rendering
    pd.set_option('display.max_rows', 30)

    print("Recommendations for user: ", user_id)

    item_list = get_recommendations_response['itemList']
    print(item_list)
    recommendation_list = []

    for item in item_list:
        title = get_restaurant(item['itemId'])
        recommendation_list.append(title)
        
    recommendations_df = pd.DataFrame(recommendation_list, columns = ['OriginalRecs'])
    recommendations_df

## Conclusion

In this post I have explain how to use AWS Personalize model to create a recommendation engine. 
Thank you for reading..

