# PE6201 RAG Course Q\&A Assistant

This system answers revision questions using only authorised course materials.  
If the materials do not contain the answer, it says so instead of guessing.

## Product

Persona: Tom, a third-year undergraduate reviewing for mid-term exams at night, who needs quick access to course content and has no technical knowledge of LLM systems.

Input: A natural language question about the course.

Output: An answer grounded in the course materials, or the fixed message "No relevant course content found".

Architecture:  
Question -> Embed -> Retrieve top-k chunks -> Check similarity against threshold -> If below, abstain -> If above, generate answer from the retrieved chunks.

External intelligence:

- OpenRouter for the LLM API.
- openai/gpt-4o-mini for generation.
- sentence-transformers/all-MiniLM-L6-v2 for embeddings.

Metrics targeted and reached:

- Grounded answer accuracy: 82.5 percent after manual review.
- Abstention rate: 73.3 percent.
- Raw LLM baseline: 37.5 percent accuracy, 0 percent abstention.

## Files

- PE_RAG_Course_QA.ipynb: the full notebook, runnable in Google Colab.
- eval_set.csv: 55 evaluation questions across two courses.
- eval_results.csv: the model output and verdict for each question.

## Data

The corpus for this project is synthetic. I wrote the two course documents and the 55 evaluation questions by hand, as substitutes for real course materials that cannot be redistributed. Real NTU course materials are not licensed for public redistribution, and using them would raise copyright and PDPA concerns. No copyrighted material was copied. All answers were fixed in eval_set.csv before any model call.

Coverage of the evaluation set: 15 factual, 15 explanatory, 10 edge, 10 out-of-scope, 5 ambiguous. Out-of-scope questions are there to test abstention.

## Evaluation

The two core metrics are grounded answer accuracy and abstention rate. The evaluation set has 55 questions, 40 answerable and 15 that should be abstained on. Answers were written by hand before any model call, and the set was held out from prompt tuning.

The abstention threshold was tuned from 0.45 down to 0.15. Accuracy rose as the threshold fell, from 20.0 percent at 0.45 to 47.5 percent at 0.15. Abstention stayed at 73.3 percent from 0.35 downward, and was 80.0 percent at 0.45. The results at 0.15 and 0.20 were the same, so I chose 0.20 as the final value.

Measured results:

- RAG at threshold 0.20: 47.5 percent accuracy by string matching, 82.5 percent after manual review.
- RAG abstention rate: 73.3 percent.
- Raw LLM accuracy: 37.5 percent.
- Raw LLM abstention rate: 0 percent.

The gap between string matching and manual review is itself a finding, because a correct answer can be phrased in different words. On manual review, 13 answers marked wrong by string matching were in fact correct. The raw LLM never abstains, which is exactly the silent failure the system is designed to prevent.

## How to run

1. Open the notebook in Google Colab.
2. Set your OpenRouter API key as a Colab secret named OPENROUTER_API_KEY. Get a key from openrouter.ai if you do not have one.
3. Run all cells from top to bottom.

## Limitations

The dataset is synthetic and cannot tell us how the system performs on real NTU course materials. Some answers sitting near a chunk boundary were not retrieved correctly, which is a chunking problem.
