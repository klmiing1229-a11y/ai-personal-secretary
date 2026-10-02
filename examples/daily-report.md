# Example: one evening's report

The user is a university student building a small tutoring business on the side. Their `strategy.md` says the
current phase is "get the first 3 paying students", and the indicators that count are **parent conversations**
and **trial lessons booked**.

## The report

**User:** Secretary, daily report: 2 lectures and 3h of library work for tomorrow's quiz. Then 2h redesigning my
tutoring flyer and logo. Messaged one parent from the school WhatsApp group about a trial lesson, no reply yet.

## The reply

```text
MIXED

The quiz prep was a real obligation, so the day was never going to be mostly business. But of the roughly two and
a half free hours, two went to the flyer and logo, and one message went to a parent. The phase is about getting
students, and a new logo doesn't get you one.

7-day: 4 parent conversations · 1 trial lesson booked

Tomorrow: Send two more parent messages before touching the flyer again.
```

## What it writes to `logs/2026-10.md`

```markdown
## 2026-10-02

**Raw report:**
2 lectures and 3h of library work for tomorrow's quiz. Then 2h redesigning my tutoring flyer and logo.
Messaged one parent from the school WhatsApp group about a trial lesson, no reply yet.

**Verdict:** MIXED

**Indicators:**
- parent conversations: 0 (1 message sent, no reply yet)
- trial lessons booked: 0

**Reason:** Study was legitimate, but most free time went to branding instead of finding students.
```

## Why this verdict

- **Not DRIFTED:** one market-facing action happened, and the quiz was a real obligation.
- **Not ON TRACK:** one message after two hours of design doesn't make it a good allocation.
- **The sent message is not counted as a conversation:** a message without a reply is not a conversation, and the
  secretary never rounds up.
