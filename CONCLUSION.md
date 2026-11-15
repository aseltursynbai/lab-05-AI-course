# Lab 05 — Conclusion

## 1. Which layer changed your results the most?

Layer 1 (`validate=True`) changed my results the most. Before adding this layer (in the `bare` run), the agent got `16/24` correct, `4` wrong, and `4` no answer, using `51` model calls and `39536` tokens. With Layer 1 enabled, the results moved to `18/24` correct, `6` wrong, and `0` no answer (with `62` model calls and `48708` tokens). This layer changed what the agent *can do*, because catching invalid tool arguments (`ToolError`) let the model retry and find two more correct answers instead of giving up. In contrast, Layer 2 changed nothing (`18/24` correct, `6` wrong, `0` no answer), and Layer 3 only changed what the agent *admits* by turning two wrong answers into refusals (`18/24` correct, `4` wrong, `2` no answer).

## 2. One failure the full harness did not fix

One question that the full harness (layer 3) still failed is `q18` (how many percentage points CPI inflation fell in Jambyl from 2022 to 2025), which ended as `UNVERIFIED` (no answer). Looking at its trace, step 1 succeeded when `get_series` returned `16.1` and `10.6`, but at step 2 the model passed `(16.1 - 10.6)/100` to `calculate` instead of simple subtraction, getting `0.055`. After the grounding check rejected `0.55`, the model repeated the exact same wrong formula and got stopped. I would add a prompt rule clarifying that percentage points are a direct difference (`end - start`) without dividing by 100. Adding extra notes costs more tokens on every request (in my notes test, tokens rose from `60116` to `66436`) and can cause new failures on other questions, like `q20` dropping from `correct` to `wrong`.

## 3. Workflow or agent for the monthly ranking?

For Dala Insight's monthly ranking, I would ship the fixed workflow because the task has a known, fixed shape. In Part 1 on the 15 fixed questions, the workflow achieved `13/15` accuracy compared to only `9/15` for the bare agent. It was also much cheaper and faster: the workflow used `15` model calls and `3852` tokens in `15.8` seconds, whereas the agent needed `26` model calls and `19835` tokens in `47.8` seconds. I would choose the agent only for open-ended questions where the exact sequence of steps is not known in advance, such as the 9 open questions in Part 1 where the workflow cannot run at all and the agent solved `7/9`.

---

I wrote this file myself, without any AI tool.
