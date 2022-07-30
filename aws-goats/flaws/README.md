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

### Level 2
Level 2 is at http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud

The next level is fairly similar, with a slight twist. You're going to need your own AWS account for this. You just need the free tier.