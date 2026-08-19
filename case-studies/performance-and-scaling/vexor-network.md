---
description: Reducing unnecessary API work
---

# Vexor Network

Vexor Network was preparing for its first users, and the application made repeated API requests for data that did not always need to be fetched again.

This created unnecessary backend work and made the application feel slower.

I added caching on the client side so previously retrieved data could be reused instead of requesting it again. Under expected load, this reduced API response times by roughly **40%**, while also reducing backend load.

The goal was not to make the backend more complicated. It was to stop doing work that did not need to be repeated.

