# Lesson 1 Notes — Balerica Legacy Bank

## Run 1 — i-01cdb6c7e18632eda (2026-11-01)

Output line from this run: `INFO:     Started server process [36769]`

This was my first run on the first instance. I looked up account 100001, posted payment TXN0001 for 125.50, checked `data/payments.dat`, activated the venv and started uvicorn. It ran smoothly with no errors.

What I understood this time: (add your own line)

## Run 2 — i-027eb63af7da53b93 (2026-11-02)

Output line from this run: `INFO:     Started server process [37756]`

The steps ran smoothly with no errors. This time I also looked up a missing account (999999) and checked `echo $?`, which returned 4.

What I understood this time: (add your own line)

## Run 3 — i-0da0f04315a20e6bc (2026-11-02)

Output line from this run: `INFO:     Started server process [36248]`

The run was smooth with no errors. I double-checked my directory: `ls` in my home folder showed only SEIR-Foundations, and `ls` in /opt/balerica-bank showed agents, app, cobol, data, logs, swift and venv.

What I understood this time: (add your own line)

## Run 4 — i-099df47e8170f56c0 (2026-11-02)

Output line from this run: `INFO:     Started server process [36294]`

No errors on this run. I opened a second window to test the running server, and the log shows `127.0.0.1:44418 - "GET /status HTTP/1.1" 200 OK`. I also tested the missing account 999999 again and got return code 4.

What I understood this time: (add your own line)

## Run 5 — i-04ad4dee6df873a89 (2026-11-03)

Output line from this run: `INFO:     Started server process [35831]`

Same sequence as before: lookup of account 100001, a posted payment (TXN0001, 125.50), the ID line, then uvicorn. It ran smoothly with no errors, and the server started on the first try.

What I understood this time: (add your own line)

## Run 6 — i-0f3e401fca2cc39a9 (2026-11-03)

Output line from this run: `INFO:     Started server process [35714]`

No errors on this run. I tested the missing account 999999 again and got return code 4 before posting the payment and starting uvicorn.

What I understood this time: (add your own line)

## Run 7 — i-0b861c9ff48a1f067 (2026-11-03)

Output line from this run: `INFO:     Started server process [35766]`

No errors on this run, and the sequence was the same as run 6: missing account 999999 returned 4, then the payment and uvicorn. I used this instance for the COBOL practice and the git clone below.

What I understood this time: (add your own line)

## Mistakes I made

1. In my first return code 8 attempt I typed `TXN-XX-0001`, using the placeholder `XX` instead of my own initials, so I had to redo it with `TXN-VW-...`.
2. When I first checked my GitHub branch I put the README in the wrong place (top level, then `Module-1/lesson1_ec2`) instead of `module1/lesson1_ec2`, so the clone's `ls` did not show `lesson1_ec2`.
3. I ran `ls` on the lowercase folder name `seir-foundations-homework/module1`, but the repo cloned as `SEIR-Foundations-homework`, and Linux paths are case-sensitive.

## Commands I now know by heart

- `cd /opt/balerica-bank`
- `source venv/bin/activate`
- `whoami && TZ='America/New_York' date && ec2-metadata -i && uptime -s`
- `uvicorn app.main:app --host 0.0.0.0 --port 8000`
- `./cobol/bin/account_lookup 100001`
- `echo $?`

## COBOL practice

Run used: Run 7, Instance ID `i-0b861c9ff48a1f067`

There are 3 accounts in `data/accounts.dat`: 100001 (Alice Johnson), 100002 (Robert Smith), 100003 (Maria Garcia). All three are `ACTIVE`, so I could not do the bonus test of posting to a non-active account.

### Return codes (my TXN IDs start with VW)

**Return code 0 (success)**

Command: `./cobol/bin/account_lookup 100001` then `echo $?`

```
ACCOUNT=100001
NAME=Alice Johnson
STATUS=ACTIVE
BALANCE=5000.00
CURRENCY=USD
0
```

Meaning: the lookup worked and the account was found.

**Return code 4 (account not found)**

Command: `./cobol/bin/account_lookup 999999` then `echo $?`

```
ERROR=ACCOUNT NOT FOUND
ACCOUNT=999999
4
```

Meaning: the account does not exist, and the program reports it without crashing.

**Return code 8 (business rejection), zero amount**

Command: `./cobol/bin/payment_post TXN-VW-0002 100001 0.00 USD TEST-ZERO` then `echo $?`

```
TXN=TXN-VW-0002
ACCOUNT=100001
AMOUNT=0.00
CURRENCY=USD
STATUS=REJECTED
REASON=AMOUNT MUST BE GREATER THAN ZERO
8
```

**Return code 8, amount bigger than the balance**

Command: `./cobol/bin/payment_post TXN-VW-0003 100001 999999.00 USD TEST-BIG` then `echo $?`

```
TXN=TXN-VW-0003
ACCOUNT=100001
AMOUNT=999999.00
CURRENCY=USD
STATUS=REJECTED
REASON=INSUFFICIENT FUNDS
8
```

Meaning: the request was understood, but a business rule said no.

**Return code 12 (environment failure)**

Command: `cd ~` then `/opt/balerica-bank/cobol/bin/account_lookup 100001` then `echo $?`

```
ERROR=CANNOT OPEN data/accounts.dat
FILE-STATUS=35
12
```

Meaning: the program looks for `data/accounts.dat` relative to the folder it is run from. From my home folder that file does not exist, so the program could not open it (`FILE-STATUS=35`) and exited with 12. The bank's data was fine; the program was run from the wrong place.

**A successful payment (for comparison)**

Command: `./cobol/bin/payment_post TXN-VW-0001 100001 5.00 USD TEST-ZERO`

```
TXN=TXN-VW-0001
ACCOUNT=100001
AMOUNT=5.00
CURRENCY=USD
STATUS=POSTED
REASON=APPROVED
0
```

### Ledger decode (by hand)

Column layout: 1-6 account id, 7-36 name, 37-44 status, 45-56 balance, 57-59 currency.

| Account | Raw balance (12 characters) | Dollar amount |
|---------|-----------------------------|---------------|
| 100001 Alice Johnson | `000000500000` | 5000.00 |
| 100002 Robert Smith | `000000275000` | 2750.00 |
| 100003 Maria Garcia | `000000810000` | 8100.00 |

Why there is no decimal point in the file: the last two digits are the cents, and the decimal point is implied by the field's definition (the layout), not stored as a character. So `000000500000` is 500000 cents, which is 5000.00. Keeping the point out keeps every field a fixed width and the amount a whole number.

### payments.dat

My journal lines:

```
TXN-VW-0001         100001000000000500USDPOSTED  TEST-ZERO
TXN-VW-0002         100001000000000000USDREJECTEDTEST-ZERO
TXN-VW-0003         100001000099999900USDREJECTEDTEST-BIG
```

After my payments I looked up account 100001 again: `BALANCE=5000.00`. It did not change, even though `TXN0001` (125.50) and `TXN-VW-0001` (5.00) were `POSTED`.

My best guess why: `payments.dat` is a journal, a record of each payment and whether it was posted or rejected. The balance in `accounts.dat` is a separate record, and I think it gets updated by a later step instead of by `payment_post` itself, the way real banks post transactions first and apply them to balances afterward.
