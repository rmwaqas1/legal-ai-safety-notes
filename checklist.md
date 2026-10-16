# Failure-mode checklist

I review each feature that touches the model, or that produces a document that
will be filed or sworn, against these questions.

## Fabricated authority
- [ ] Can the feature output a case, statute or rule citation? If so, is it checked against a copy of the source before the lawyer relies on it?
- [ ] Is every quotation compared word for word with the cited source, with alterations (ellipses, brackets, emphasis) shown?
- [ ] Is a pinpoint past the end of the source flagged?
- [ ] Is "not found" shown as a flag, never silently dropped?

## Sworn evidence
- [ ] Does every statement in a draft come from the deponent or a source document?
- [ ] When a fact is missing, does the system ask instead of filling it in?
- [ ] Are names, dates and numbers ever produced by the model rather than copied from a source? They should not be.
- [ ] Once sworn, is the document sealed rather than edited?

## Procedure and deadlines
- [ ] Does every deadline or form shown cite the rule it comes from?
- [ ] Is the rule's wording quoted from the official source with its date, not paraphrased?
- [ ] Is there a way to tell when a rule has been amended since it was encoded?

## Confidentiality and privilege
- [ ] Does any client data leave the machine? (Default: no.)
- [ ] Can the model move a document out of privilege? (It must not.)
- [ ] Is anything the model learns free of client names, numbers and quotations?
- [ ] Are logs kept per case, never mixed across cases, and never put into a prompt?

## Overreliance
- [ ] Does the model's output take effect only after the lawyer confirms it?
- [ ] Is there an Undo?
- [ ] Do the model's suggestions start unticked?
- [ ] Are irreversible actions (filing, payment, signing, redaction) gated behind an explicit human step the model can't take?

## Evaluation
- [ ] Is there a test case for the failure, on synthetic data?
- [ ] Is there a case where the right answer is to refuse or ask?
- [ ] Does the case run against the real model, not only a stub?
