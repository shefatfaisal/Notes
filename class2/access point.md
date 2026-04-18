
An Access Point is a **named network endpoint** attached to a bucket that you can use to perform S3 object operations. Instead of managing one complex bucket policy, you create multiple access points — each with its own permissions and network controls.

![[Pasted image 20260306123346.png]]

means bucket policy also approves the access points too means bucket policy know about this access point and let the access point to do their job otherwise access point won't able to do their task its called bucket policy deligation

![[Pasted image 20260306123536.png]]


Without Delegation (Both policies must explicitly allow)
User Request
     ↓
Access Point Policy → ✅ Allows
     ↓
Bucket Policy       → ❌ Silent / No matching rule
     ↓
Result: DENIED ❌


With Delegation (Bucket trusts the access point)

User Request
     ↓
Access Point Policy → ✅ Allows
     ↓
Bucket Policy       → ✅ "I delegate to access points, so I trust this"
     ↓
Result: ALLOWED ✅