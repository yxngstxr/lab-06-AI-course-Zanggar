# REPORT — what the measurement showed, who it affects, and whose responsibility it is

## 1. Measurement read-out

I tested the bare screening agent on 200 matched pairs (400 model calls) in Part 1. Each pair changed one attribute while keeping the other profile information the same. The name-related tests used `null_name` as a floor; the university test used `null_university`.

| Attribute | Pairs | Flips | Flip rate (95% CI) | Mean absolute score difference |
|---|---:|---:|---:|---:|
| Name language | 40 | 2 | 5.0% [1.4%, 16.5%] | 3.12 |
| Gender-coded name | 40 | 4 | 10.0% [4.0%, 23.1%] | 1.12 |
| University | 40 | 1 | 2.5% [0.4%, 12.9%] | 2.50 |
| Null name floor | 40 | 0 | 0.0% [0.0%, 8.8%] | 2.00 |
| Null university floor | 40 | 1 | 2.5% [0.4%, 12.9%] | 2.12 |

The observed shortlist rates were 90% for female-coded names and 80% for male-coded names. This was the opposite of my initial expectation that female-coded names would be disadvantaged. By name language, shortlist rates were 96.4% for Kazakh names, 92.3% for Russian names and 96.2% for English names. Flagship-university profiles had a 92.5% shortlist rate and regional-university profiles had a 90% rate.

None of the three real attributes clearly exceeded its corresponding floor. The gender-coded-name flip rate was numerically higher than the null-name floor, but the confidence intervals overlap. The university flip rate was the same as its floor. These results show that this experiment did not establish a reliable attribute effect; they do not establish that bias is absent. With 40 pairs per comparison, small or moderate effects could remain undetected. The floors estimate variation caused by changing the relevant field to a neutral value, but they do not test every possible proxy, interaction, prompt, model or real-world applicant population.

In Part 3, the harness blinded the model to `name`, `university`, `age` and `consent_to_automated_processing`. All five paired flip rates became 0/40. This is expected because the paired prompts became identical after those fields were withheld; it is not proof that the system is fair. Across 400 profiles, 122 decisions changed after the harness was enabled, and 175 scores changed. The mean absolute score change was 14.54 points. The harness produced 236 shortlist outcomes, 124 reject outcomes and 40 escalations in the measured Part 3 run. The planned borderline band covered 163/400 profiles (40.8%) before the model was run, but only 40/400 (10%) were actually escalated after blinding changed the scores. The model made 400 calls in both runs; counterfactual checking was off, so the harness did not add model calls in Part 3.

Part 4 tested refusal boundaries. The harness refused four of the six test scenarios: an impermissible ethnicity-ranking purpose, a minor applicant, an applicant without consent, and invalid input. The ordinary applicant and the instruction embedded in the name field were escalated as borderline cases. The custom refusal rule did not refuse the prompt-injection example in this run. The complete `decisions.jsonl` file contains 407 records, including 4 refusal outcomes and 43 escalation outcomes. These totals include the measured run and the later test records; they should not be confused with the Part 3-only totals above.

The optional prompt-order probe is not included in these findings because no result for that probe was supplied for this report. The findings apply to this synthetic test set, this prompt, and `Qwen/Qwen2.5-3B-Instruct`; they cannot establish how another model, prompt or real applicant population would behave.

## 2. Who is affected

A wrongly rejected applicant loses the opportunity to be considered for funding. That can affect the applicant's education plans, finances and family, and the loss may be difficult to reverse if deadlines pass. A wrongly shortlisted applicant may occupy a limited review place or award slot that could otherwise go to another applicant. The harm is therefore not symmetrical: a false rejection directly excludes one person, while a false shortlist can also displace someone else.

The Part 1 results connect this risk to the tested groups without proving that any group was systematically harmed. Gender-coded names produced 4 flips in 40 pairs (10.0%), compared with 0/40 for the null-name floor; the observed shortlist rates were 90% for female-coded names and 80% for male-coded names. Name language produced 2 flips in 40 pairs (5.0%), while the observed shortlist rates differed across Kazakh, Russian and English names. University produced 1 flip in 40 pairs (2.5%), equal to the null-university floor. Because the confidence intervals overlap and the sample is small, I cannot claim that these differences represent a stable disadvantage or advantage. If an undetected effect exists, applicants whose names, language backgrounds or institutions are treated differently would bear the cost.

Other affected parties include applicants who decide not to apply because they expect the process to be unfair; staff who must review escalated cases; the fund, which may make poor allocation decisions or lose trust; and applicants from institutions or backgrounds that are poorly represented in the model's training data. Repeated use could compound small errors across application cycles. A rejection may also influence later decisions if it is copied into another record or used as a signal by another institution.

The blinding control reduces the model's direct exposure to names and university labels, but it does not remove all possible proxies or guarantee that the remaining features are fair. It also changes decisions: 122 of 400 decisions changed between the bare and harness runs. That means both applicants who might have been shortlisted before and applicants who might now be rejected or escalated need monitoring. The 40 human escalations in Part 3 also create work for reviewers, and the fact that only 10% were escalated after the score distribution changed means many cases outside the band were not independently re-checked.

## 3. Professional responsibility

As the developer, I am responsible for testing the harness against relevant groups and realistic edge cases, documenting what it does and does not protect against, and not describing the system as unbiased or legally compliant based only on this small experiment. Before deployment, I would require a larger and more representative evaluation, repeated tests across prompts and model versions, tests for proxy variables and intersections between attributes, and a documented process for correcting inaccurate input and challenging an outcome. I would also require a stop condition: suspend automated screening if monitoring finds unexplained group disparities, a material rise in invalid or unstable decisions, or a failure of the human-review process.

In this lab I implemented input validation, blinding, refusal rules, logging and borderline escalation, and measured the before-and-after outcomes. I did not implement an appeal interface, applicant notification, role-based access to the log, retention/deletion automation, tamper-evident logging, or a guaranteed human review of every shortlist and rejection. I also did not successfully refuse the prompt-injection example in the name field. These are known gaps, not features that I can claim to have delivered.

The deploying institution is responsible for defining a lawful and narrow purpose, deciding the actual eligibility criteria, appointing trained reviewers, providing accessible explanations and an appeal route, setting retention and access rules, and making the final funding decision. The institution must not treat a model score as an objective measure of merit. If it asks to remove blinding or human review merely to save time, I should explain the measured changes and the risks, document the request, and refuse to certify the resulting system as adequately tested. Professional responsibility requires me to communicate those limits even when they conflict with the institution's preference for speed or lower cost.
