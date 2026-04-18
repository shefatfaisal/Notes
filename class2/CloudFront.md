If we have a application in a region accessing it from other region gives us high latency. For that **AWS** comes up with a service called **CloudFront**.

Every  region has something called Edge location. so whenever we use CloudFront our application cache store in EdgeLocation.

**EdgeLocation** is managed by AWS.

In CloudFront we create **Distribution**

Create Distribution = origin? Mumbai : ELB/S3
where to cache? Entire world, Asia, US, Europe


and aws has its own network named **CDN(Content Delivery Network)**
aws taking the content and delivering to the destination using **CDN**

**CloudFront** can store both static and dynamic if we want to delete something not only in our region but also across whole country **EL** we can do that by **Invalidate Cache**

Based on the **TTL** thats how long data cached.

- min = 0 sec
- default = 1 day
- max = 1 year


without CF
user -> boom.com -> route53 -> ELB -> EC2

with CF

user -> boom.com -> R53 -> CF/EL -> ELB -> EC2
![[Pasted image 20260115105247.png]]
