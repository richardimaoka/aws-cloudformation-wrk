## A demo to run wrk and save the result file to S3

This demo runs wrk on Amazon EC2, save its results into `.json` along with EC2 metadata,
then save the `.json` file into S3.

It's based on my previous work https://github.com/richardimaoka/aws-ec2-to-s3.

## How to run the demo?

### Pre-requisite
- Install AWS CLI, and configure it with an API key/secret having AWS Admin access
- Create an S3 bucket to save the result file
- (Create an EC2 key-pair in advance for SSH access, as Cloudformation doesn't create key pairs )
  - (Yes, the whole purpose of this demo is build a processing pipeline not using SSH, but in case of troubleshooting, you would still want SSH)

### Then, just run this

- `git clone https://github.com/richardimaoka/aws-cloudformation-wrk.git`
- `cd aws-cloudformation-wrk`
- run `./local-commands.sh` from your local PC
  - this will create a Cloudformation stack in your AWS account from the template file, `ec2-to-s3.cf.yml` in this repository
  - and send an AWS System Manager command to the created EC2 instance within the Cloudformation stack
  - the command executes `remote-ec2-to-s3.sh` on the EC2 instance, and save the resulting file `results.txt` to S3

## Things to note

- [AWS Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html) is needed to execute remote commands on EC2 via AWS CLI. Without using SSH's interactive shell to run arbitrary commands, the workflow is more streamlined
  - [Official video resources for AWS System Manager](https://www.youtube.com/watch?v=zwS8lssaY_k&list=PLhr1KZpdzukeH5jKyYi55ef9tEWAllypB)
  - After `local-commands.sh` sent the AWS System Manager command, go to the following page and check the command status:
https://console.aws.amazon.com/ec2/v2/home?#Commands:sort=CommandId

- Pay attention to file permissions when you run **git-cloned** files on EC2
  - That was already taken care for this repository, but here's the details:
  - To run `remote-ec2-to-s3.sh` and `remote-gen-text.sh` on EC2, I needed to change the file permission in the following way:
  - [Look here for more background](https://medium.com/@akash1233/change-file-permissions-when-working-with-git-repos-on-windows-ea22e34d5cee).
  - TL;DR
    - `git ls-files --stage` to check
    - `git update-index --chmod=+x 'name-of-shell-script'` to update

## Cloudformation Stack

Illustrated by [Cloudformation Designer](https://console.aws.amazon.com/cloudformation/designer/home):
(S3 bucket is not here as I created it in advance, but you can create a new bucket from Cloudformation too if you extend my `ec2-to-s3.cf.yml`)

![](./EC2Instance-designer.png)
