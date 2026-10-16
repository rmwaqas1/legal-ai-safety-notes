# Safety in legal AI: what I'm building and what worries me

*Affidavi.ai · October 2026*

I'm building two tools for litigation work in Ontario.

**Affidavi** is desktop software for a litigator's documents. It handles the
affidavit of documents (Rule 30.03, Forms 30A and 30B), evidence management,
linked affidavits and exhibits, redaction, a check of the authorities in a
factum, and commissioning affidavits by video under O. Reg. 431/20. It is built
for lawyers and their staff. It includes an assistant, Artie, that works
through the same tools the lawyer uses. Artie runs on a 9B open-weights model
that runs on the lawyer's own Mac.

**CourtPilot** supports Ontario civil procedure work, such as deadlines, legal
tests and court documents under the Rules of Civil Procedure.

The code is private because it handles client documents and is pre-release. I'm
publishing my safety analysis, my failure-mode checklist and synthetic test
cases here instead.

## Why legal is a high-stakes domain

A wrong answer in legal work ends up as a filed document, a sworn statement or
a missed deadline, and the person it harms often can't spot the error.

## Failure modes I design against

1. **Fabricated authority.** Invented cases, wrong citations, or real cases
   cited for things they don't say. Courts have already sanctioned lawyers for
   this, and Ontario now requires counsel to certify the authorities in a
   factum (r. 4.06.1(2.1)).
2. **Unfaithful sworn evidence.** An affidavit is a statement under oath. If a
   model fills a gap or attributes words to the deponent that they never said,
   that is a legal and ethical problem, not a typo.
3. **Confident procedural errors.** Limitation periods, filing deadlines and
   forms change with rule amendments. A stale or wrong answer can end a claim.
4. **Confidentiality and privilege.** Client material has to stay where the
   client and the lawyer expect it. A privileged document produced by mistake
   can't be taken back.
5. **Overreliance.** Fluent, polished output discourages checking.

## What I actually do about it

**The model runs on the lawyer's machine.** Affidavi never goes online. Its
only connection is to its own model server on `127.0.0.1`, with a one-time
key. It has no telemetry, update checks or cloud APIs. The one online feature,
driving the court portals, is off until the lawyer turns it on. Even then the
model can't type a password and can't press submit, pay, delete or consent. It
can only ask, and the lawyer answers yes or no.

**The model proposes. The lawyer decides.** Nothing the assistant suggests
takes effect until the lawyer confirms it, and every change comes back with
Undo. The assistant's memory keeps patterns of work, never a case. A request
containing a name, number or quotation isn't learned.

**No invented facts.** The assistant is instructed never to invent names, dates
or numbers. Evaluation cases check that it asks instead of filling a gap.

**Privilege only moves one way.** The model can mark a document as possibly
privileged ("Schedule B, to check"). It can never take a document out of
Schedule B unless the lawyer says so.

**Forms are quoted, not paraphrased.** The affidavit of documents follows the
official Form 30A/30B wording word for word.

**Authorities are checked against the source.** The factum check reads each
citation and quotation in a factum. It then compares them with the copies of
the cases and statutes the lawyer supplies. It flags a quotation that isn't in
the cited case, words that were altered, a pinpoint past the last paragraph,
and a citation that doesn't match its copy. The lawyer can sign off only once
every blocking flag is resolved.

**What is sworn is sealed, never rewritten.** After a document is commissioned,
the signed scan is sealed only if its pages match the copy that was printed.
Otherwise it is flagged for the lawyer's decision. The commissioner's signature
needs Touch ID. The assistant can never give it.

**Redaction never guesses.** Nothing is removed unless the reviewer has seen it
marked. The model's suggestions start unticked. Checking the redacted copy is a
separate step, on the reviewer's word.

**Tested on made-up documents only.** Every test generates its own synthetic
documents, so no client file is ever needed. Beyond the unit tests, more than
90 scripted cases run against the real local model. They cover linking,
affidavits of documents, redaction, evidence and PDF editing. Several of them
are refusal cases: the right answer is to ask the lawyer or decline.

## In this repository

- [`checklist.md`](checklist.md): the failure-mode checklist I review each
  feature against.
- [`test-cases/`](test-cases/): synthetic test cases (a fabricated citation, a
  missing fact in an affidavit, a privilege trap, a wrong deadline) with the
  behaviour I expect. All names, citations and facts in them are invented.

## What still worries me

- Small local models are less capable than frontier models, and evaluation only
  covers the cases I thought of.
- A check that passes can make people trust the output more than it deserves.
  The tools flag; they don't certify.
- Rules change. Anything that encodes a rule needs a date and a source the
  lawyer can check.
