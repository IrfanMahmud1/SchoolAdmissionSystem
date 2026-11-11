# Assumptions and Rationale

The assignment explicitly asks for reasonable interpretations to be documented here. Update this file when a decision is actually made.

1. **Student identity is a supplied identifier, not a login system.**
   - The brief specifies `X-Student-Id` for student-specific endpoints and does not require authentication.
   - The UI will let the user enter an ID and remember it locally for the demo. This is not represented as production authentication.

2. **Payment is simulated.**
   - The brief describes a mock fee payment/confirmation flow.
   - Clicking the UI action invokes the confirm API; no real money or payment gateway is involved.

3. **Admin access is simplified.**
   - The brief requires school admin and guardian tasks to be available through UI but does not specify an admin authentication protocol.
   - Implement a clearly labelled demo/admin view without claiming it is secure authentication. Explain the limitation in README.

4. **Expiry is enforced during reads and state-changing operations.**
   - An expired HELD record is treated as EXPIRED and its seat is released logically even if no scheduled background job has run.
   - A background cleanup job is optional and must not be required for correctness.

5. **Seat counts are derived from persisted application states.**
   - HELD and unexpired holds consume seats; CONFIRMED consumes seats; EXPIRED and CANCELLED do not.
   - If the final implementation uses a different persisted counter strategy, document how consistency is maintained.

6. **Class name is the human-readable name of a class/shift offering.**
   - The sample is `Class 6 - Morning Shift`; model each admission offering as one capacity-bearing class record unless the PDF indicates otherwise.

7. **Conflict response for unavailable capacity or duplicate application.**
   - Use HTTP 409 for business conflicts such as no seats and duplicate active/confirmed application, unless the exact PDF specifies otherwise.

8. **No lottery; first successful hold wins.**
   - The brief says admission is first-come-first-served. Database serialization determines which concurrent request wins; no lottery or queue priority is introduced.

9. **Browser persistence stores only the demo student identifier.**
   - Application data is reloaded from `GET /me/holds`; do not treat local storage as the authoritative application database.
