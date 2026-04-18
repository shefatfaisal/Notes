![[Pasted image 20260306162625.png]]

we create key pair using ssh-keygen too 

there is a small modifications we need to done which is when we create bucket policy 
by default
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Statement1",
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:ListBucket",
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteBucket"
      ],
      "Resource": "arn:aws:s3:::shefatt"
    }
  ]
}
```
in resource its mean its cant access objects inside the bucket so we have to  replace with this

```json
"Resource": ["arn:aws:s3:::shefatt", "arn:aws:s3:::shefatt/*"]
```

so we can access all the objects inside the bucket