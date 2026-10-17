# Instructor feedback follow up October 2026

Professor Sanford recommended broader authentication, authorization, and failure testing.
Run: npm ci --ignore-scripts && npm test
Result: 38 passing tests (3 existing validation cases plus 35 added security and HTTP cases).

The added suite exercises a real ephemeral Express HTTP server, the actual API router,
Passport local strategy, JWT verification and generation, and PBKDF2 password methods.
Only Mongoose persistence methods are stubbed. Negative authorization cases assert
that persistence is never called. This is not a live MongoDB integration run or a browser
end-to-end run. It does not measure coverage percentage.

Covered: missing/malformed/expired/future tokens, incorrect signing key, algorithm,
issuer and audience; POST/PUT/DELETE authentication; authorized writes and input
allowlisting; missing records; duplicate records; safe database failures; real
Passport success and generic login failures; registration validation and errors;
salt uniqueness; correct/wrong passwords; legacy compatibility; malformed hash length.

Remaining security scope: no administrator/editor distinction is implemented.
Public registration currently creates users whose valid tokens can write trips.
Authentication-only write protection is not an administrative enrollment policy.
Account provisioning, role authorization, revocation, throttling, browser flows,
and live-database behavior require additional work before production deployment.

AI use: ChatGPT assisted in creating and running these tests and writing this report.
