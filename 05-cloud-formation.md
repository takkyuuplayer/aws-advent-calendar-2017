# [AWS CloudFormation](https://aws.amazon.com/cloudformation/?nc1=h_ls)

This is 5th entry of my advent calendar in 2017.

## Motivation

I'd not like to click management console anymore.
It is time to provision.

Let's create a S3 bucket `test.takkyuuplayer.com` with the same setting as `takkyuuplayer.com` for exercise.

## Let's use it

### Set up command line environment

I made an IAM user having programatic Full access to CloudFormation.

```
-> $ brew install awscli
-> $ aws configure
```

### Set up `test.takkyuuplayer.com`

By following below document, I created a S3 bucket first.

> [Learn Template Basics \- AWS CloudFormation](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html)


```json
{
    "Resources" : {
        "TpBucket" : {
            "Type" : "AWS::S3::Bucket"
        }
    }
}
```

```
-> $ aws cloudformation create-stack --stack-name hello --region us-east-1 --template-body file://$PWD/templates/hello-bucket.json
{
    "StackId":"..."
}
-> $ aws cloudformation list-stacks --region us-east-1
{
    "StackSummaries": [...]
}
```

After `StackStatus` became `CREATE_COMPLETE`, I confirmed a new bucket exists.

```
-> $ aws s3 ls
2017-12-05 19:14:56 hello-tpbucket-8grqfozqehch
...
```

Then I updated the bucket name to `test.takkyuuplayer.com` by setting Properties.

> [AWS::S3::Bucket \- AWS CloudFormation](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html#aws-properties-bucket-prop)

```json
{
  "Resources": {
    "TpBucket": {
      "Type": "AWS::S3::Bucket",
      "Properties": {
        "BucketName": "test.takkyuuplayer.com"
      }
    }
  }
}
```

```
-> $ aws cloudformation update-stack --stack-name hello --region us-east-1 --template-body file://$PWD/templates/hello-bucket.json
{
    "StackId":"..."
}
-> $ aws s3 ls
2017-12-05 19:14:56 hello-tpbucket-8grqfozqehch
2017-12-05 20:30:58 test.takkyuuplayer.com
...
```

`test.takkyuuplayer.com` was created. The remaining `hello-tpbucket-8grqfozqehch` was deleted automatically after a while.

Finally, I set up redirection.

```json
{
  "Resources": {
    "TpBucket": {
      "Type": "AWS::S3::Bucket",
      "Properties": {
        "AccessControl": "Private",
        "BucketName": "test.takkyuuplayer.com",
        "WebsiteConfiguration": {
          "RedirectAllRequestsTo": {
            "HostName": "takkyuuplayer.com",
            "Protocol": "https"
          }
        }
      }
    }
  }
}
```

```
-> $ aws cloudformation update-stack --stack-name hello --region us-east-1 --template-body file://$PWD/templates/hello-bucket.json
{
    "StackId":"..."
}
```

Now redirection is working

```
-> $ curl -I http://test.takkyuuplayer.com.s3-website-us-east-1.amazonaws.com


HTTP/1.1 301 Moved Permanently
x-amz-id-2: 1I1BZdQ1PAxZ08NY41u/M62mCPrvV35/y2COxOa1BuH4lN+XuGyTRh1pSP0dwt4VuS7Tkfwt7P8=
x-amz-request-id: D41FA22631D7CD07
Date: Wed, 06 Dec 2017 02:42:20 GMT
Location: https://takkyuuplayer.com/
Content-Length: 0
Server: AmazonS3
```

## Consideration

### Terraform vs CloudFormation

For syntax, I prefer Terraform syntax than CloudFormation but I decided to use CloudFormation as it is official tool from Amazon. For the difference,

> [CloudFormation vs\. Terraform \| cloudonaut](https://cloudonaut.io/cloudformation-vs-terraform/)

is well explained. The web page says CloudFormation cannot handle existing resource but [CloudFormer](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-cloudformer.html) is available for the purpose now.

## Conclusion

Compared to other AWS produces I have used in this advent calendar, starting CloudFormation took more time. GUI is intuitive and it's good for beginning stage.

It'll be nice to manipulate remaining products via CloudFormation with checking GUI on management console.

## P.S.

Leave any comment on
https://github.com/takkyuuplayer/aws-advent-calendar-2017/pull/7
