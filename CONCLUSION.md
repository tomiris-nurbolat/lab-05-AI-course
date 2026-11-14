# Lab 05 — Conclusion
# Nurbolat Tomiris
## 1. Which layer changed your results the most?

Layer 1 changed my results the most. Bare -> layer 1, out of 24:
correct 16 -> 18, wrong 4 -> 6, no answer 4 -> 0.

It changed what the agent can do, not only what it admits. In layer 1 a tool error goes back to the model
as a message, so the model can try again. Because of this, q23 and q24 alias questions changed
from no answer to correct. But q21 and q22 changed from no answer to wrong, so the agent now gives
a wrong answer where before it gave no answer.

Layer 2 changed nothing. The results, the model calls 62 and the tokens 48708 were the same as layer 1.

Layer 3 did not add correct answers still 18. It only changed what the agent admits:
wrong 6 -> 4 and no answer 0 -> 2 (q18 and q22 became UNVERIFIED). It also cost more: 74 model calls
and 60116 tokens.

## 2. One failure the full harness did not fix

The question is q08: which region had the lowest average monthly wage in 2024.
It is wrong even with layer 3.

In the trace, the first step was fine: the model called compare_regions and got the list of all regions.
The mistake was in the next step. The model should take the last row of the list, because we need
the lowest value. Instead, it answered "Jambyl". Its first reply also had no ANSWER line
and layer 3 only made it add one.

The harness did not see the mistake. "Grounded" only checks that the name is in a tool output
and compare_regions lists all regions, so every region name passes.

I would add a code check for rank questions: for "highest" the answer must be the first row
of the compare_regions result for "lowest" the last row.
Cost: one more model call and more tokens for each rejected answer. A new way to fail: the code can read
"highest" or "lowest" in a wrong way and reject a right answer.
Gain: it works only for rank questions, so it could fix only q08 and q11, which is 2 of 24 questions.

## 3. Workflow or agent for the monthly ranking?

I would ship the fixed workflow. The monthly question (highest and lowest region for one indicator
in one year) has a fixed shape and the workflow is made for this.

My numbers from Part 1 on the 15 fixed-shape questions:
- Accuracy: workflow 13 correct, 0 wrong, 2 no answer. Agent bare 9 correct, 2 wrong, 4 no answer.
- Model calls: workflow 15, agent 26.
- Tokens: workflow 3852, agent 19835.
- Time: workflow 15.6 seconds, agent 48.0 seconds.

On the rank questions q07-q11 the workflow had no failures. Its only 2 failures were q21 and q22
alias questions. The bare agent was wrong on q08 and q11.

I would choose the agent for open questions, for example the percent change of one region between
two years, because the workflow cannot take them. On the 9 open questions the agent bare had
7 correct, 2 wrong, 0 no answer.

I wrote this file myself, without any AI tool.
