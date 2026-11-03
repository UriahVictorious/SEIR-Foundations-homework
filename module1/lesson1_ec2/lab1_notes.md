# Lesson 1 Notes — Balerica Legacy Bank

## Run 1 — i-01cdb6c7e18632eda (2026-11-01)

Output line from this run: `INFO:     Started server process [36769]`

- Ran straight from the runbook.
- No errors.
- Starting to understand how SWIFT works.


## Run 2 — i-027eb63af7da53b93 (2026-11-02)

Output line from this run: `INFO:     Started server process [37756]`

- Ran from the runbook again.
- No errors.
- No new understanding.


## Run 3 — i-0da0f04315a20e6bc (2026-11-02)

Output line from this run: `INFO:     Started server process [36248]`

- Ran from the runbook, minus the test entries.
- No errors.
- Found out that I could do the run without the extra entries.


## Run 4 — i-099df47e8170f56c0 (2026-11-02)

Output line from this run: `INFO:     Started server process [36294]`

- I checked my directory in between entries and checked information in the second window.
- Still no errors.
- Found out that I could still run separate processes, and the second window shows up in the bank window's data.


## Run 5 — i-04ad4dee6df873a89 (2026-11-03)

Output line from this run: `INFO:     Started server process [35831]`

- Went back to the runbook.
- Repeated a download by mistake.
- Found out that if you download information into the ec2, it won't download it again.


## Run 6 — i-0f3e401fca2cc39a9 (2026-11-03)

Output line from this run: `INFO:     Started server process [35714]`

- Did all the steps, including the test entries again.
- No mistakes this time.
- I understand better how the bank and the customer interface interact.

## Run 7 — i-0b861c9ff48a1f067 (2026-11-03)

Output line from this run: `INFO:     Started server process [35766]`

- I did all my other steps, using this instance.
- I put all my mistakes below.
- I learned not to do your homework late at night! Lol!

(Write at least 3 sentences.)

## Mistakes I made

1. In my first return code 8 attempt I typed `TXN-XX-0001`, using the placeholder `XX` instead of my own initials, so I had to redo it with `TXN-VW-...`.
2. When I first checked my GitHub branch I put the README in the wrong place (top level, then `Module-1/lesson1_ec2`) instead of `module1/lesson1_ec2`, so the clone's `ls` did not show `lesson1_ec2`.
3. I ran `ls` on the lowercase folder name `seir-foundations-homework/module1`, but the repo cloned as `SEIR-Foundations-homework`, and Linux paths are case-sensitive.

(Edit or replace these so they are in your own words and are things that really happened to you.)

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

Meaning: (write in your own words why running from the wrong folder breaks it. Hint: the program looks for `data/accounts.dat` relative to the folder you run it from.)

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

Why there is no decimal point in the file: The ledger can only use numerals, and the last 2 numbers will be the cents

### payments.dat

My journal lines (re-copy these from your terminal so the spacing is exact):

```
TXN-VW-0001         100001000000000500USDPOSTED  TEST-ZERO
TXN-VW-0002         100001000000000000USDREJECTEDTEST-ZERO
TXN-VW-0003         100001000099999900USDREJECTEDTEST-BIG
```

After my payments I looked up account 100001 again: `BALANCE=5000.00`. It did not change, even though `TXN0001` (125.50) and `TXN-VW-0001` (5.00) were `POSTED`.

Best guess: `payments.dat` is a journal, a record of each payment and whether it was posted or rejected. The balance in `accounts.dat` is a separate record, and I think it gets updated later instead of by a `payment_post` itself, the way real banks post transactions first and apply them to balances afterward.
