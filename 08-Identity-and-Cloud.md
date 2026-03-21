# Identity and Cloud Concepts

## IAM and Identity Protocols

- OAuth 2.0: delegated authorization framework.
- OpenID Connect (OIDC): identity layer on top of OAuth 2.0.
- Auth0: identity provider supporting modern auth and MFA patterns.

## Access Control Models

- RBAC: role-based access (admin, viewer, member).
- ABAC: attribute-based access (department, ownership, subscription tier).

## Row-Level Security (RLS)

- RLS in PostgreSQL enforces tenant-aware data access at table policy level.
- Helps avoid manually appending tenant filters to every query.

## Cloud and Platform Notes

- Forge (Laravel): dashboard-driven VPS setup and management.

## Messaging and SMTP

- SMTP is used for outbound mail transport.
- External sender to Microsoft tenant typically resolves MX and sends to M365 protection endpoint over port 25.
- Internal authenticated sending generally uses `smtp.office365.com` and may require MFA/app password.

## Related Operational Notes

- PBX/IPBX: internal and external voice routing systems.
- SIP providers bridge IP telephony and PSTN connectivity.
