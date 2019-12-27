# External references

Below I have listed some interesting references regarding different topics. This way if I encounter the technology I can watch or read someone else's hacks for inspiration.

## AWS

- BSidesMCR 2019: Exposing AWS With FlAWS - Mike Lehan ([video](https://youtu.be/eRc6lrAV6Rg)) *Entry level*: Focuses around the CTF `flaws.cloud` to demonstrates the security implications of different AWS misconfigurations, how to exploit and mitigate them. Very useful things to test buckets.  
    - S3 misconfiguration allowing unathenticated access to bucket's web root files through anonymous use of the aws cli.
    - Canned ACL misconfiguration allowing authenticated users to AWS 