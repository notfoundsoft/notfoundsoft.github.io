---
layout: default
title: pgp
permalink: /pgp/
---

# `$ gpg --list-keys notfound`

```
pub   ed25519/AA18BCA70A410258 2026-05-07 [SC] [expires: 2028-05-06]
      C80C3C166DEFC9D78C0323BEAA18BCA70A410258
uid                            notfound <notfoundsoft@gmail.com>
sub   cv25519/5957F2B8BEC1F4F5 2026-05-07 [E]  [expires: 2028-05-06]
```

**Fingerprint:** `C80C 3C16 6DEF C9D7 8C03  23BE AA18 BCA7 0A41 0258`

[Download `pubkey.asc`](/assets/pubkey.asc)

## Import

From this site:

```
curl -fsSL https://notfoundsoft.github.io/assets/pubkey.asc | gpg --import
```

From a keyserver:

```
gpg --keyserver hkps://keys.openpgp.org --recv-keys C80C3C166DEFC9D78C0323BEAA18BCA70A410258
```

## Verify

After import, confirm the fingerprint matches before trusting:

```
gpg --fingerprint notfoundsoft@gmail.com
```

Expected output should match the block above. If it doesn't, do not trust the key.

## Encrypt mail to me

```
gpg --encrypt --armor -r notfoundsoft@gmail.com message.txt
```

## Verify a signed commit or release

```
git verify-commit <commit-sha>
git verify-tag    <tag>
```

## Notes

- `[SC]` primary key signs and certifies. Long-lived identity.
- `[E]` cv25519 subkey handles encryption. Rotates independently.
- Expires 2028-05-06. Watch this page for renewal or rotation announcements.
- If this key is revoked, a notice will appear at the top of [contact](/contact/) and on the GitHub profile README.
