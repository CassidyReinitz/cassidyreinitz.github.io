# Code Review Expansion Recording Script

Cassidy Reinitz | CS 499 | October 2026

## Recording and editing instructions

This is a substantive supplement to the existing 20 minute 8 second review, not a completed new recording. Target approximately 10 to 12 additional minutes after rehearsal. Speak naturally, show the named source, and spend time tracing the examples. Do not stretch audio or add silent padding. Retain the original pre-enhancement review. Introduce this supplement as deeper analysis of the preserved originals followed by a clearly labeled final verification update. Append it to the original video or link both parts with an explicit combined viewing order. Verify the final actual runtime before submission.

Before recording, open the original Travlr route, user model and trip controller; original CS 300 ProjectTwo.cpp; original Grazioso CRUD module; the enhanced security test file; and graph benchmark CSV. Do not display environment files, passwords or tokens. Use synthetic records only.

## Segment One Scope and review method

On screen: original project folders beside enhanced folders. Approximately one minute.

This supplement expands the reasoning behind my original code review. I will first revisit preserved source from Travlr Getaways, the Advising Assistance Program, and Grazioso Salvare. I will distinguish existing functionality from limitations and explain why particular enhancements address the problem. I will then show the final testing evidence added in response to instructor feedback. The later results are a follow-up, rather than part of the original pre-enhancement recording.

My audience includes developers who might maintain these systems and stakeholders who need to understand their behavior. For each issue, I am asking what the code currently does, what can go wrong, what change would help, and what evidence would establish that the change works. This makes the review useful for a decision instead of simply listing features.

## Segment Two Travlr request and trust boundaries

On screen: Original_Travlr_Getaways/app_api/routes/index.js and controllers/trips.js. Trace POST and PUT for about 30 seconds.

In the original router, reading trips is public and writing trips requires a Bearer token. That distinction is already useful, so I should not describe the original application as having no authentication. The token check is embedded in the route module, however, making that responsibility harder to reuse and test independently. The enhanced version separates verification into middleware and constrains algorithm, issuer, and audience.

In the original trip controller, individual properties are copied from the request. That is an explicit field selection, so it would be inaccurate to say the controller blindly copies every property. The problem is that these selected values still need consistent normalization and checks for format, length, date, price, and image filename. Repeating assignments across creation and update also makes it easier for those paths to drift apart.

A validation boundary should produce a known internal representation before a controller reaches persistence. The controller should then handle normal success, duplicate data, missing records, and database failures predictably. The original error responses include exception messages. Returning a stable public message avoids exposing internal details while allowing maintainers to investigate separately.

## Segment Three Authentication and authorization limits

On screen: original and enhanced app_api/models/user.js; enhanced routes. Approximately one minute.

The original user model uses a random salt and PBKDF2, which are existing strengths. Its work factor is one thousand iterations, and verification compares hash strings directly. The enhancement raises the work factor, stores that setting with new records, and uses a constant-time comparison. The compatibility fallback matters because old records were derived using the old iteration count. Verifying them with only the new count would reject correct passwords.

Token verification establishes that the presented token satisfies the signing and claim rules. It does not by itself establish an administrator or editor permission policy. The current application lets registered users obtain tokens that permit trip writes. Public account registration therefore remains an important enrollment limitation for an administrative application. I have not implemented distinct administrator and editor roles, and I should not claim those roles in the final portfolio.

The tests demonstrate the implemented authentication boundary. A production permission design would require a trusted account-provisioning policy and explicit permissions in addition to valid tokens.

## Segment Four Advising representation and failure behavior

On screen: original ProjectTwo.cpp map declaration, printCourseList, loader, and menu. Trace a missing prerequisite and a cycle on paper or in an editor.

The original catalog uses an ordered map. Its lookup is logarithmic, not a repeated linear search. The alphabetical listing copies the keys and sorts them even though map iteration is already ordered. That is a specific example of redundant work. The enhanced design chooses an unordered map for expected constant-time lookup and performs sorting where presentation needs it.

Prerequisites are initially stored as strings attached to a course. That supports displaying requirements but does not establish that the requirements form a valid dependency system. A missing prerequisite can make a record unusable. A cycle, such as one course depending on another that eventually depends on the first, means no valid completion sequence exists.

An adjacency-list graph represents those relationships directly. Edges run from a prerequisite to its dependent course. Completing a prerequisite then reduces the dependent course's remaining count. Depth-first traversal detects a cycle, and Kahn's algorithm produces an order only for a valid graph.

The loader also needs to reject duplicate identifiers, malformed records and missing references. The enhanced implementation clears the active catalog when loading begins and refuses partial invalid results. It does not preserve the previous valid catalog on a failed reload. That distinction makes the actual failure behavior clear.

## Segment Five Complexity and measurable scaling

On screen: enhanced CourseCatalog.cpp hasCycle and buildGraph, then evidence/graph_benchmark.csv. Approximately one and a half minutes.

A complexity explanation must describe the implementation that is actually present. The DFS traversal visits vertices and edges in linear time. However, this hasCycle method first sorts vertex identifiers, so the complete method includes an expected V log V term. Graph construction also sorts dependent lists. Ignoring those sorts would understate the implementation cost.

The min-priority queue in topological sorting makes selection deterministic. Each vertex enters and leaves the queue once, while each edge updates a count, giving expected E plus V log V time under ordinary hash-table assumptions. That deterministic order helps users and makes repeated results easier to compare.

To follow the instructor's recommendation, I tested sparse, chain, wide, and dense generated graphs. Twelve configurations ran three times each. The largest sparse cases contain four thousand courses; the largest dense case contains one thousand courses and four hundred ninety-nine thousand five hundred edges. The benchmark independently checks every prerequisite against the output order and requires repeated schedules to match.

These are local measurements on synthetic inputs. They support the observed behavior, not a guarantee for every machine or arbitrarily deep recursion.

## Segment Six Grazioso data integrity and presentation

On screen: original CRUD_Python_Module.py read, update and delete; enhanced pagination and aggregation. Approximately one and a half minutes.

The original data-access module checks that inputs are dictionaries and rejects empty write filters. Those checks reduce some mistakes, but they do not enforce the allowed fields or the shape of each record. Its update and delete methods can affect several records. When an interface action intends one animal record, a single-record operation provides a more controlled boundary.

The read method converts the complete cursor into a Python list. As matching data grows, that approach increases memory use and the amount of information passed to the dashboard. Bounded server-side pagination limits each request. A stable tie-breaker is also necessary when several records share the same displayed sort value.

Pagination changes how a chart should be computed. If the table shows ten rows, a breed chart based only on those rows would describe the page rather than the matching population. Aggregating all matches in MongoDB keeps the summary meaningful while the table remains bounded.

Database-enforced permissions and schema validation matter because another client can bypass Python checks. Testing direct writes is stronger evidence than showing only an interface that hides a button.

## Segment Seven Verification update and outcomes

On screen: Travlr evidence/security_tests.txt, advising benchmark validation, then portfolio. Allow about one minute for evidence navigation.

The final Travlr run passes thirty-eight tests. The added tests exercise real Express routes, Passport, token verification, and password methods, with persistence stubbed. They check invalid and expired tokens, rejected writes before database calls, normal authenticated operations, generic login failures, duplicates, missing records, and sanitized errors. These are not live MongoDB or browser end-to-end tests.

The advising regression suite passes ten tests, and the benchmark checks every generated dependency. A separate four-thousand-course cycle is rejected without leaving a partial catalog. Retained build commands, timing data and scope notes make the evidence reproducible.

Across the portfolio, these decisions support algorithmic evaluation, practical implementation, and a security mindset. Explaining the reasoning through source walkthroughs, narratives, and accessible pages supports professional communication. Clear test scope, review comments, and documented assumptions help other developers evaluate decisions. My CS 250 coursework adds the collaboration strategies behind that approach. The purpose of this review is to make the reasoning inspectable and show where further work is still needed.

## Final publishing checklist

Record the supplement with audible narration and readable source. Confirm the combined actual runtime is approximately 30 minutes. Review the start, middle and end for audio and screen alignment. Upload the expanded video to the user's chosen host and replace or supplement the current link. The existing video remains unchanged until that recording and upload occur.

AI acknowledgment: ChatGPT assisted with this script, source analysis and testing. Review and understand each explanation before recording.
