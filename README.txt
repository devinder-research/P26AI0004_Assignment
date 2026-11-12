Name - Devinder
Roll No.- P26AI0004

AI Assignment 2026 - Symbolic reasoning agent
=============================================

Files
  reasoning_agent.ipynb  complete implementation (parsers, reasoning engines, experiments, submission writer)
                         with the outputs of the final run saved in the cells
  report.pdf             representation, algorithms, experiments, reasoning traces
  submission.csv         columns id,label (label = True/False), 7404 rows, written by the last notebook cell

How to run (Google Colab or local Jupyter, Python 3.10+)
  1. Open reasoning_agent.ipynb and choose Runtime > Run all (internet needed: the first cells download
     RuleTaker, FOLIO and CounterBench from Hugging Face; no API key needed).
  2. The experiment cells print accuracy per dataset; the "Final results" cell prints the overall accuracy
     and the total inference time; the last cell writes submission.csv.
  3. To predict on a hidden/private test file, set TEST_PATH (csv / json / jsonl) in the last cell and re-run it.
     Expected columns:  RuleTaker-style -> context + question;  FOLIO -> premises-FOL + conclusion-FOL;
     CounterBench -> question + given_info.  The output is again id,label.

Final results on the public data (from the saved notebook run)
  RuleTaker (5000 test queries)   99.94 %      1.1 s
  FOLIO (train+validation, 1204)  81.73 %    173.5 s   (True vs not-True)
  CounterBench (1200)             94.58 %      0.1 s
  Overall                         96.11 %  (7116 / 7404)      total inference time 174.7 s

Method (no generative AI is used to produce any answer)
  RuleTaker / NL KB : rule-based parser -> facts + rules with variables -> forward chaining with
                      stratified negation-as-failure (final engine); backward chaining (SLD resolution with
                      unification) is implemented as a cross-check.
  CounterBench      : parser -> structural causal model -> abduction, do-intervention, prediction
                      (exact enumeration over root variables). Only "and" makes an AND gate; observations
                      ("X occur(s)", "X not occur(s)") are parsed; when the observation contradicts the
                      "instead of" claim, only the observation is conditioned on.
  FOLIO             : first-order formulas -> CNF (skolemization) -> set-of-support resolution with
                      unification and factoring. The parser accepts unicode/numbered/dotted symbols, matches
                      symbols case-insensitively and repairs malformed annotations (unbalanced brackets,
                      trailing text, arrow variants).
  Three settings (negation semantics, CounterBench root prior / list rule) are chosen automatically
  by the notebook on public development data; they are not tuned on any private data.

Note: submission.csv contains the predictions for the public datasets used in the notebook (ids are row
numbers); the private test set was not available during development.
