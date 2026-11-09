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

1.
2.
3.
4.
5.
6.
7.
8.