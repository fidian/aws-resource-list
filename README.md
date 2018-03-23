AWS Resource List
=================

Ever have the need to list every resource in every region for an account? We've wanted something like this to check for rogue machines after keys were leaked. It's useful to use when decommissioning an account, yet keeping it around for future machines. Finally, it doesn't hurt to be able to audit what you have running.

What does this do? It writes ARNs and other information to the screen. Here's an example with several rows of data removed. You'll notice the `fake_arn` lines, which lists resources that aren't assigned an ARN (as far as I know).

    $ ./aws-resource-list --parallel=20
    Account ID: 111222333444
    Alias:  cws-dev-tools-uat
    Regions:    global,ap-northeast-1,ap-northeast-2,ap-south-1,ap-southeast-1,ap-southeast-2,ca-central-1,eu-central-1,eu-west-1,eu-west-2,eu-west-3,sa-east-1,us-east-1,us-east-2,us-west-1,us-west-2
    arn:aws:iam::111222333444:server-certificate/example.com
    arn:aws:iam::111222333444:user/fidian
    arn:aws:iam::111222333444:user/rumkin
    arn:aws:iam::111222333444:policy/rumkin-user
    arn:aws:iam::111222333444:role/aws-service-role/ops.apigateway.amazonaws.com/AWSServiceRoleForAPIGateway
    arn:aws:iam::111222333444:role/aws-service-role/rds.amazonaws.com/AWSServiceRoleForRDS
    arn:aws:iam::111222333444:role/rumkin-aws
    arn:aws:iam::111222333444:role/expire-snapshots
    arn:aws:iam::111222333444:instance-profile/rumkin/rumkin
    fake_arn:aws:ec2:ap-northeast-1:111222333444:vpc:vpc-12345678
    fake_arn:aws:ec2:ap-northeast-1:111222333444:acl-12345678
    arn:aws:kms:ap-northeast-1:111222333444:alias/aws/rds
    arn:aws:rds:ap-northeast-1:111222333444:secgrp:default
    fake_arn:aws:ec2:ap-northeast-2:111222333444:prefixList:pl-12345678:prefixListName/com.amazonaws.ap-northeast-2.dynamodb

The list goes on and on and on. It also takes a very long time because it makes hundreds of API calls for each region. Parallelization of the operations is nearly required.


Installation
------------

Well, you can just download the `aws-resource-list` and `bpm.ini` files if you like, but I prefer using `git` to clone the repository. Pick your favorite.

After that, you must use [BPM](https://github.com/bpm-rocks/bpm) to install dependency libraries.

    $ bpm install

After that, all you need is Bash installed on your system and you can run it.

Lastly, this depends on two tools. You need the [AWS CLI](https://aws.amazon.com/cli/) so it can make all of these API calles. Also, [`jq`](https://stedolan.github.io/jq/) is used to process JSON on the command line.


Usage
-----

* `--help` - Shows you what options are available.
* `--account=NUM --role=ROLE` - Use these two settings together if you want to switch to another role. The account number is mandatory; you can not use an alias.
* `--mfa` - Prompt the user for an MFA code and create an authenticated session. When used with a role in another account, the MFA authorization happens before switching roles.
* `--parallel=NUM` - When running AWS operations, try to run this many requests at once. Minimum is 1. When unset, this defaults to the number of CPU cores on the system. I suggest going way higher than that, perhaps using a number between 10 and 20.
* `--regions=REGION,REGION,...` - Comma separated list of regions to scan. In addition to the typical ones, there's a special `global` region that is used for IAM and S3.

About the option processing: You must use an `=` to separate an option. Only `--parallel=10` is valid. Using a space, such as with `--parallel 10`, is not going to work.


License
-------

The software is licensed under an MIT style license. [Read full text.](LICENSE.md).
