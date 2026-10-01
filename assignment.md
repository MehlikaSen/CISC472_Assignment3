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

**how a login becomes a cookie the browser sends back**:

When the user submits their email and password, the app checks whether the credentials are correct. If they are correct, the app creates a random session token, stores it in the database, and sends it to the browser in a cookie named `hold_session`. The browser automatically sends this cookie with future requests. The app reads the cookie, finds the matching session in the database, and knows which user is signed in. The cookie is protected with `HttpOnly` and `SameSite=Lax` so it is harder to steal.


**the difference between the public board and a booking detail**

The public board shows available office hour slots, so students can choose a slot. A booking detail is private and is shown after signing in. It contains information about one booking, such as the student, TA, location, and any private note from the TA. Students can view their own booking details, while TAs can view bookings for their slots.

**which actions change server state (book, cancel, transfer, logout)**

Booking changes the database by creating a booking for a student and slot. Canceling deletes that booking. Transferring changes the booking’s student to another student. Logging out deletes the current session token from the database and removes the session cookie from the browser. These actions change server data, while simply viewing the board or a booking detail only reads data.

**what the device-handoff link on My bookings is doing**

The device-handoff link was intended to let a signed-in user open their bookings on another browser without logging in again. It added the user’s session ID to the URL as `?sid=...`. The app used that value to identify the user, but this was unsafe because anyone who received the link could use the session ID to access the account. After the fix, the app no longer accepts session IDs from URLs and uses the protected cookie instead.