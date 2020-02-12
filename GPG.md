---
tags:
  - GnuGP
---

# GnuPG
```
gpg --full-gen-key
gpg --list-secret-keys
gpg --export-secret-key -a "Support <support@nsoftware.com>" > secret.key
gpg --export -a "NsoftwareTest <support@nsoftware.com>" > public.key
```