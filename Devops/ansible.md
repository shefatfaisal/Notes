![[Pasted image 20260317014401.png]]

![[Pasted image 20260317015302.png]]



![[Pasted image 20260319152037.png]]

We can identify particular task and can run particular task

we can do tags in the task


when we learn we do
````yml
ansible-playbook name.yml --tags name
ansible-playbook name.yml --skip-tags name // skip the particular tags
````




![[Pasted image 20260401173927.png]]

# lookups
![[Pasted image 20260401174355.png]]
![[Pasted image 20260401174337.png]]


![[Pasted image 20260401182619.png]]


![[Pasted image 20260401183710.png]]
![[Pasted image 20260401183720.png]]




![[Pasted image 20260401184304.png]]
![[Pasted image 20260401204739.png]]

![[Pasted image 20260401204843.png]]

![[Pasted image 20260401204903.png]]

# ansible cheetsheet

![[ansible_concepts_cheatsheet.html]]

![[ansible_inventory_and_roles_deep_dive.html]]
# SSH Password Authentication Fix on RHEL/AWS EC2

## Problem
`ssh-copy-id` fails with:
Permission denied (publickey,gssapi-keyex,gssapi-with-mic)

Even after setting in `/etc/ssh/sshd_config`:
- PermitRootLogin yes
- PasswordAuthentication yes

## Root Cause
AWS EC2 RHEL instances have override files in:
/etc/ssh/sshd_config.d/

The file `50-cloud-init.conf` sets:
PasswordAuthentication no

This **overrides** the main sshd_config silently.

## Fix
1. Check override files:
   ls /etc/ssh/sshd_config.d/

2. Fix the culprit file:
   sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' \
   /etc/ssh/sshd_config.d/50-cloud-init.conf

3. Restart sshd:
   systemctl restart sshd

4. Verify effective config:
   sshd -T | grep -i passwordauth
   # Should show: passwordauthentication yes

5. Now ssh-copy-id will work:
   ssh-copy-id root@<target-ip>

## Key Lesson
Always verify the **effective** sshd config using:
sshd -T | grep -i passwordauth
Not just the sshd_config file itself.

## Files involved
- /etc/ssh/sshd_config          ← main config
- /etc/ssh/sshd_config.d/50-cloud-init.conf  ← AWS override (culprit)
- /etc/ssh/sshd_config.d/50-redhat.conf
- /etc/ssh/sshd_config.d/40-redhat-crypto-policies.conf