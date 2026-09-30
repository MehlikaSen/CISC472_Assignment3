**Hosted application:** [https://cisc472-assignment3.onrender.com] (https://cisc472-assignment3.onrender.com)

# Session ID exposed in link

**Location:** `app.py`, in `load_user()`
`templates/mine.html`, in the Device handoff link, the session token is added to the URL as `?sid=...`.

**Trigger:**

1. Sign in to the hosted application.
2. Open **My bookings** and click the Device handoff link.
3. Observe that the address contains `/me?sid=...`.
4. Open the complete link in a private window or another browser.
5. The app opens the user’s bookings without requesting a password because the `sid` value acts as the login token.


*Example:* A signed-in user opens their bookings and shares the link with someone else. Because the link contains the user’s session ID, the other person can open it and view the user’s bookings without entering a password.

**The Fix**: Remove the session ID from the device-handoff URL. The app should store the session token only in a secure cookie with `HttpOnly` and `SameSite=Lax` enabled. This prevents the token from appearing in shared links, browser history, and screenshots. Any old exposed session tokens should also be invalidated.

**how a login becomes a cookie the browser sends back**

**the difference between the public board and a booking detail**

**which actions change server state (book, cancel, transfer, logout)**

**what the device-handoff link on My bookings is doing**