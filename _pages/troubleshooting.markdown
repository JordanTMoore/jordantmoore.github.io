---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

permalink: /troubleshooting/
layout: home
title: Troubleshooting
description: Get help diagnosing issues and details on how to fix more frequently encountered issues with IBM TRIRIGA Assistant.

sidebar:
  nav: "docs"
---

## Common Problems

If a popup appears saying “You do not have permission to access this page” when you access the Workplace Services apps, then the user doesn’t have a security group that has the permissions documented in step K.

---

If the chat icon appears but you don’t see the introduction similar to what is shown in the image at the very top of this doc, then check that the user you are using has a primary location set in the user’s profile.

---

If the chat icon doesn’t appear in the bottom right corner of the Workplace Services app, then check for errors using the Console tab of the Inspector (right click in webpage and choose Inspect).

---

If you find that you reservations made through the assistant do not appear in the Workplace Services home page (/p/web/workplaceServices), then check that the security groups assigned to that user have an Organization set. The security groups mentioned in Step D don’t have an Organization set by default. There might also be issues with the Organization set on the assistant account as well as Organization set on the user and the buildings.

---

If service requests aren’t getting created, check that you provided a primary location and organization to the assistant account.
