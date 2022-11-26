# Deploy a React app to AWS S3 - using webpack

## Step 1: Generate a React build (webpack)
Webpack is `create-react-app`'s bundler. It already has a command for us to generate the build:
1. `npx create-react-app spa-to-s3 --template typescript`
2. `npm run eject`
3. `npm run build` 
4.  Confirm the `/build` folder exists with an `index.html` file.

## Step 2: Set up S3
_This step assumes the prerequisite of creating an AWS account is completed._

The goal for this step, is to be able to host a static web app.
_More info can be found [here](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)_. 
1. [Log in](https://signin.aws.amazon.com/signin) to AWS -> Head to AWS [S3 Service](https://s3.console.aws.amazon.com/s3/get-started), and [create a bucket](https://s3.console.aws.amazon.com/s3/bucket/create). 
2. Give it any name e.g. `canada-frontend-development`. Note the default region, `ca-central-1` in my case.
3. By default, you'll find the extra config with default options:
   1. ✅ Access Control Lists = `disabled`
   2. 📝 Block Public Access = `block all public access`. (This is the only default setting we change, we uncheck the `block all public access` checkbox to __allow all access__, since we want to deploy a static [SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA)).
   3. ✅ Bucket versioning = `disabled`
   4. ✅ Tags = `none`
   5. ✅ Encryption = `disabled`
4. Confirm the bucket exists in the [list](https://s3.console.aws.amazon.com/s3/buckets). 
5. Click on the bucket we just created, find "Properties" in the top tab. Scroll all the way down until "Static website hosting".
6. Edit "Static website hosting" to _Enable_ it. Type `index.html` under the "Index document" input, as suggested.

## Step 3: Deployment through CLI
_This step assumes the prerequisite of having the AWS CLI installed is completed._

We cannot prompt a browser to authenticate in the CLI through an user account.

By default, AWS creates a Root user for us (All permissions), hence, the "0 Users" we see (0 [IAM](https://www.redhat.com/en/topics/security/what-identity-and-access-management-iam) Users).

1. Head to the [IAM dashaboard](https://us-east-1.console.aws.amazon.com/iamv2/home) -> and [create an IAM User](https://us-east-1.console.aws.amazon.com/iam/home#/users$new)
2. Pick your username, __select the 2 checkboxes__ for _Access Key_ and _Password_. Note that you can now log in through this user, aside from the Root user.
3. To handle permissions, AWS will suggest to "Create a Group". We can name it `s3-deployments`, and check the box for the policy `AmazonS3FullAccess`.
4. We skip the optional tags, and confirm the IAM User creation. 
5. We now have access to:
   1. Access Key ID
   2. Secret Access Key
6. Running `aws configure` in our terminal, will prompt for: `Access Key Id`, `Secret Access Key` and `region`. The output format can be left as `[]`
7. We will run `aws s3`, the [docs](https://docs.aws.amazon.com/cli/latest/reference/s3/) here states how to build the s3 URL: `s3://<mybucket>/<mykey>`.  The bucket name I used was `canada-frontend-development`, and the Encryption Key is none, since encryption is disabled at the moment.
8. The deployment command using `aws s3` should look as: `aws s3 sync ./build/ s3://canada-frontend-development`
9. Going back to the "Properties" tab, under "Static website hosting", will now display an URL ending in `.amazonaws.com`! ...Which will return `403`, because we haven't set the access policy yet.
10. For this, find "Bucket policy" in the same "Properties" tab, and paste the following:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::canada-frontend-development/*"
        }
    ]
}
```
(`canada-frontend-development` replace with your bucket name).

This `JSON` file specifies that, whoever (`*`) wants to `GetObject` (access a file), from our bucket, will be `"Allow"`(ed).

Try that URL under "Static website hosting" one more time.