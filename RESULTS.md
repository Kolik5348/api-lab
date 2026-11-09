# API lab results

## Part 0

- Step 0.1, the number of pizza names: 18
- Step 0.3, status code of the request without a token: 401
- Step 0.4, the name of your pizza: The Heavy Calzone
- Step 0.12, results of the three runs without a session (optional): run 1: failed (401), run 2: passed, run 3: failed (401)
- Step 0.16, blocks of "how many of the three tools are excluded": 0 tools, 1 tool, 2 tools, 3 tools (all)

## Defect report

- Title: Excluding all three tools makes POST /api/pizza answer 500 with an empty body
- Request: `POST https://quickpizza.grafana.com/api/pizza` with the header `Authorization: token <token of a logged-in user>` and the body `{"excludedTools": ["Knife", "Pizza cutter", "Scissors"]}`
- Expected result: Status 400 and a JSON error message, because no pizza can be made without any of the three tools. The product owner says that a request that no pizza can satisfy gets 400 and a JSON error message, never 500.
- Actual result: Status 500 Internal Server Error with `Content-Length: 0`, so the body is empty and there is no error message. Confirmed with curl (`HTTP/1.1 500 Internal Server Error`) and with pytest (`assert 500 == 200` for the input `['Knife', 'Pizza cutter', 'Scissors']`). The inputs that exclude 0, 1 or 2 tools all answer 200 with a pizza, so the failure appears only when all three tools are excluded.
- Severity: Medium. The server fails on input that any customer can send, and an app that calls the API gets no message that it can show to the user. The defect does not damage data, and it appears only in one rare case, so I do not rate it High.

## Part A

- Step A4, status codes when the other user reads, changes and deletes: read = 404, change = 400, delete = 400

## Part B

1. Tests against the real server can find a 500 with an empty body (Defect 1) and a 401 at login when the load balancer sends the request to another server (Step 0.12). A stub hides both. Unit tests with stubs are still useful because they are fast, do not need the network, and point to the exact function that is wrong.

2. Each test creates its own user so that nobody else can change its data while it runs. Ratings made with the public token belong to everyone. In Project 2 I will create my own customer, cart or booking with made-up names in each test, use only data my group created, and delete it afterwards.

3. A requests.Session keeps the AWSALB cookie, so the load balancer sends every request to the same server, and each server keeps its own users. In Step 0.12 I sent each request on its own and got failed (401), passed, failed (401). The test passed only when both requests happened to reach the same server, so it was flaky.

4. The block "3 tools excluded" revealed the defect: 0, 1 and 2 tools give 200, but all 3 give 500. A 500 with an empty body is worse than a 400 with a message because the app cannot tell what was wrong, cannot show the user anything useful, and cannot tell a retry from a permanent error. The test stays as xfail because it records a known defect and keeps the suite green. With strict=True the run fails when QuickPizza fixes it, which tells me to remove the mark. A deleted test would hide the defect.

5. Base choice needs 1 + 2 + 3 + 1 + 2 = 9 tests (one base test plus one for each non-base block: C1 has 3 blocks, C2 has 4, C3 has 2, C4 has 3). All combinations need 3 x 4 x 2 x 3 = 72 tests.

6. The other user got 404 for read, 400 for change and 400 for delete. I expected 403 (or 404 for all three). All attempts were refused and the rating stayed unchanged, so there is no security defect. But the codes are inconsistent, and 400 is misleading because the request is well formed. I count this as a minor defect in the API design.

7. The made-up token 0000000000000000 gets 200 OK and a list of 50 ratings that belong to other people. This is a defect, and it is serious: the API accepts a token that nobody got by logging in. On a real system a stranger could read data of other users without an account, and could probably also create, change or delete data. The API should answer 401 for an invalid token.

8. An API test checks status codes, error messages and fields better, and it runs faster and can send invalid input (such as 0 or 6 stars) that a page would never send. Only a test through the web pages can check what the user sees, such as layout, button text and whether the error message is readable.