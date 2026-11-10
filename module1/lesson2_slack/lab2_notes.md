# Lesson 2 Homework Notes — Slack Alerts

**Student:** Vince
**Workspace:** https://balericaalerts.slack.com
**Alert channel:** #all-balerica-alerts

## Script answers

**Script 2 (severity tour):** All 4 arrived because my threshold was at the lowest level (INFO) and it sends everything at or above.

**Script 3 (server time):** The server time was 5 hours ahead of my clock (UTC vs. Eastern Standard Time). The server showed 02:39 UTC on Sunday while it was about 9:39 PM Saturday for me.

**Script 4 (disk watch):** You wouldn't want constant notifications for every small change that isn't a problem, so INFO is a good design choice. It's just an "FYI."

**Script 5 (load check):** Load could spike from large, repeated transactions all at once, like a rush on the bank during an emergency.

**Script 6 (incident drill):** The ledger file being unreachable and the payments endpoint returning 500 errors deserve a 3 AM wake-up. They could affect large, important accounts, so they need to be looked at right away.

## Experiment 1: prediction vs. result

- **Setup:** Set `SLACK_ALERT_SEVERITY=CRITICAL` and reran script 2.
- **Prediction:** 0 messages would arrive (I wasn't sure why).
- **Result:** 1 message arrived (CRITICAL). INFO, WARNING and ERROR were skipped.
- **Why:** The filter sends anything at or above the threshold, and "at or above" includes equal. CRITICAL is the threshold, so it passed. The other three were below it.

## Other experiments

- **Experiment 2 (kill switch):** With `SLACK_ALERTS_ENABLED=false`, script 1 printed `Skipped.` and nothing reached Slack.
- **Experiment 3 (disk threshold):** After lowering the WARNING threshold from 75 to 20, script 4 reported 25.6% used and chose WARNING instead of INFO.
- **Experiment 4:** Restored `SLACK_ALERT_SEVERITY=INFO`, `SLACK_ALERTS_ENABLED=true`, and the `hw4_disk_watch.py` threshold back to 75.

## Difference between the two URLs (for the lab submission)

`SLACK_WORKSPACE_URL` only identifies and labels which workspace the alert belongs to, and `slack.py` validates it. It doesn't control where messages go. `SLACK_WEBHOOK_URL` is the private endpoint the terminal posts to, and it's what actually delivers the message to the channel I authorized. That's why the webhook is a secret kept in `.env`, and the workspace URL isn't.
